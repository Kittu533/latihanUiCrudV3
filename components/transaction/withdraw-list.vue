<template>
    <div class="space-y-4">
      <div class="flex items-center justify-between">
        <div>
          <h2 class="text-2xl font-bold tracking-tight">Transaksi - Penarikan</h2>
        </div>
        <div class="flex items-center gap-2">
          <div class="relative">
            <button class="bg-white border px-4 py-2 rounded-md flex items-center gap-2 hover:bg-gray-50">
              <span>Cetak</span>
              <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="lucide lucide-chevron-down"><path d="m6 9 6 6 6-6"/></svg>
            </button>
          </div>
          <button 
            class="bg-white border px-4 py-2 rounded-md flex items-center gap-2 hover:bg-gray-50"
            @click="showFilter = !showFilter"
          >
            <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="lucide lucide-filter"><polygon points="22 3 2 3 10 12.46 10 19 14 21 14 12.46 22 3"/></svg>
            <span>Filter</span>
          </button>
          <button 
            class="bg-[#4072EE] text-white px-4 py-2 rounded-md flex items-center gap-2 hover:bg-[#3060DD]"
            @click="router.push('/admin/transaction/withdraw/new')"
          >
            <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="lucide lucide-plus"><path d="M5 12h14"/><path d="M12 5v14"/></svg>
            <span>Tambah Penarikan</span>
          </button>
        </div>
      </div>
  
      <!-- Filter Panel -->
      <TransactionFilter 
        v-if="showFilter" 
        :filter="filter"
        @apply="applyFilter"
        @reset="resetFilter"
        @close="showFilter = false"
      />
  
      <!-- Data Table -->
      <div class="border rounded-lg overflow-hidden bg-white shadow-sm">
        <div class="p-4 border-b">
          <h3 class="text-lg font-medium">Data Insentif</h3>
        </div>
        <div class="p-4">
          <!-- Loading state -->
          <div v-if="isLoading" class="flex justify-center py-8">
            <div class="animate-spin rounded-full h-12 w-12 border-t-2 border-b-2 border-gray-900"></div>
          </div>
          
          <!-- Error state -->
          <div v-else-if="error" class="bg-red-100 p-4 rounded-md text-red-800">
            {{ error }}
          </div>
          
          <!-- Data table -->
          <div v-else class="overflow-x-auto">
            <table class="min-w-full divide-y divide-gray-200">
              <thead class="bg-gray-50">
                <tr>
                  <th scope="col" class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">No</th>
                  <th scope="col" class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">ID Insentif</th>
                  <th scope="col" class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Nama Guide</th>
                  <th scope="col" class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Nomor Rekening</th>
                  <th scope="col" class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Jumlah Pendapatan</th>
                  <th scope="col" class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Metode Penarikan</th>
                  <th scope="col" class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Tanggal Penarikan</th>
                  <th scope="col" class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Waktu Penarikan</th>
                  <th scope="col" class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Status</th>
                  <th scope="col" class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Aksi</th>
                </tr>
              </thead>
              <tbody class="bg-white divide-y divide-gray-200">
                <tr v-for="(withdraw, index) in withdraws" :key="withdraw.id">
                  <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-500">{{ index + 1 }}</td>
                  <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-500">{{ withdraw.id }}</td>
                  <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-500">{{ withdraw.guideId }}</td>
                  <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-500">{{ withdraw.voucherId }}</td>
                  <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-500">Rp{{ formatCurrency(withdraw.amount) }}</td>
                  <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-500">{{ withdraw.paymentMethod }}</td>
                  <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-500">{{ withdraw.transactionDate }}</td>
                  <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-500">{{ withdraw.transactionTime }}</td>
                  <td class="px-6 py-4 whitespace-nowrap">
                    <span 
                      class="px-2 py-1 inline-flex text-xs leading-5 font-semibold rounded-full"
                      :class="getStatusClass(withdraw.status)"
                    >
                      {{ formatStatus(withdraw.status) }}
                    </span>
                  </td>
                  <td class="px-6 py-4 whitespace-nowrap text-sm font-medium">
                    <div class="flex space-x-2">
                      <button @click="viewWithdraw(withdraw.id)" class="text-blue-600 hover:text-blue-900">
                        <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="lucide lucide-eye"><path d="M2 12s3-7 10-7 10 7 10 7-3 7-10 7-10-7-10-7Z"/><circle cx="12" cy="12" r="3"/></svg>
                      </button>
                      <button @click="editWithdraw(withdraw.id)" class="text-yellow-600 hover:text-yellow-900">
                        <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="lucide lucide-pencil"><path d="M17 3a2.85 2.83 0 1 1 4 4L7.5 20.5 2 22l1.5-5.5Z"/><path d="m15 5 4 4"/></svg>
                      </button>
                      <button @click="deleteWithdraw(withdraw.id)" class="text-red-600 hover:text-red-900">
                        <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="lucide lucide-trash-2"><path d="M3 6h18"/><path d="M19 6v14c0 1-1 2-2 2H7c-1 0-2-1-2-2V6"/><path d="M8 6V4c0-1 1-2 2-2h4c1 0 2 1 2 2v2"/><line x1="10" x2="10" y1="11" y2="17"/><line x1="14" x2="14" y1="11" y2="17"/></svg>
                      </button>
                    </div>
                  </td>
                </tr>
              </tbody>
            </table>
          </div>
          
          <!-- Pagination -->
          <div v-if="pagination" class="flex items-center justify-between mt-4 text-sm">
            <div>
              Halaman {{ pagination.currentPage }} dari {{ pagination.totalPages }} ({{ pagination.total }} data)
            </div>
            <div class="flex items-center space-x-2">
              <button 
                @click="handlePageChange(pagination.currentPage - 1)"
                :disabled="pagination.currentPage === 1"
                class="px-2 py-1 border rounded-md disabled:opacity-50"
              >
                <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="lucide lucide-chevron-left"><path d="m15 18-6-6 6-6"/></svg>
              </button>
              <button 
                v-for="page in pagination.totalPages" 
                :key="page"
                @click="handlePageChange(page)"
                class="px-3 py-1 border rounded-md"
                :class="page === pagination.currentPage ? 'bg-blue-500 text-white' : ''"
              >
                {{ page }}
              </button>
              <button 
                @click="handlePageChange(pagination.currentPage + 1)"
                :disabled="pagination.currentPage === pagination.totalPages"
                class="px-2 py-1 border rounded-md disabled:opacity-50"
              >
                <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="lucide lucide-chevron-right"><path d="m9 18 6-6-6-6"/></svg>
              </button>
            </div>
          </div>
        </div>
      </div>
    </div>
  </template>
  
  <script setup lang="ts">
  import { ref, onMounted, computed } from 'vue'
  import { useRouter } from 'vue-router'
  import { useWithdrawStore } from '~/store/withdraw'
  import TransactionFilter from '~/components/transaction/transaction-filter.vue'
  
  // Router and stores
  const router = useRouter()
  const withdrawStore = useWithdrawStore()
  
  // State
  const showFilter = ref(false)
  
  // Computed
  const withdraws = computed(() => withdrawStore.withdraws)
  const pagination = computed(() => withdrawStore.pagination)
  const filter = computed(() => withdrawStore.filter)
  const isLoading = computed(() => withdrawStore.isLoading)
  const error = computed(() => withdrawStore.error)
  
  // Methods
  const formatCurrency = (value: number): string => {
    return value.toLocaleString('id-ID')
  }
  
  const formatStatus = (status: string): string => {
    return status.charAt(0).toUpperCase() + status.slice(1)
  }
  
  const getStatusClass = (status: string): string => {
    const statusClasses: Record<string, string> = {
      'berhasil': 'bg-green-100 text-green-800',
      'batal': 'bg-yellow-100 text-yellow-800',
      'gagal': 'bg-red-100 text-red-800',
      'tertunda': 'bg-gray-100 text-gray-800',
      'masuk': 'bg-blue-100 text-blue-800'
    }
    
    return statusClasses[status] || ''
  }
  
  const applyFilter = (newFilter: Partial<TransactionFilter>) => {
    withdrawStore.setFilter(newFilter)
  }
  
  const resetFilter = () => {
    withdrawStore.resetFilter()
    showFilter.value = false
  }
  
  const handlePageChange = (page: number) => {
    withdrawStore.setFilter({ page })
  }
  
  const viewWithdraw = (id: string) => {
    router.push(`/admin/transaction/withdraw/${id}`)
  }
  
  const editWithdraw = (id: string) => {
    router.push(`/admin/transaction/withdraw/${id}/edit`)
  }
  
  const deleteWithdraw = async (id: string) => {
    if (confirm('Apakah anda yakin ingin menghapus transaksi ini?')) {
      try {
        // Implement delete logic here
        await withdrawStore.loadWithdraws()
      } catch (error) {
        console.error('Error deleting withdraw:', error)
      }
    }
  }
  
  // Lifecycle
  onMounted(async () => {
    try {
      await withdrawStore.loadWithdraws()
    } catch (error) {
      console.error('Error loading withdraws:', error)
    }
  })
  </script>