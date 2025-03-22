<template>
    <div class="space-y-4">
      <div class="flex items-center justify-between">
        <div>
          <h2 class="text-2xl font-bold tracking-tight">Pengembalian</h2>
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
            @click="router.push('/admin/return/new')"
          >
            <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="lucide lucide-plus"><path d="M5 12h14"/><path d="M12 5v14"/></svg>
            <span>Tambah Pengembalian</span>
          </button>
        </div>
      </div>
  
      <!-- Filter Panel -->
      <ReturnFilter 
        v-if="showFilter" 
        :filter="filter"
        @apply="applyFilter"
        @reset="resetFilter"
        @close="showFilter = false"
      />
  
      <!-- Data Table -->
      <div class="border rounded-lg overflow-hidden bg-white shadow-sm">
        <div class="p-4 border-b">
          <h3 class="text-lg font-medium">Data Pengembalian</h3>
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
                  <th scope="col" class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">ID Return</th>
                  <th scope="col" class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">ID Transaksi</th>
                  <th scope="col" class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">ID Pelanggan</th>
                  <th scope="col" class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Jumlah</th>
                  <th scope="col" class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Alasan</th>
                  <th scope="col" class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Tanggal</th>
                  <th scope="col" class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Waktu</th>
                  <th scope="col" class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Status</th>
                  <th scope="col" class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Aksi</th>
                </tr>
              </thead>
              <tbody class="bg-white divide-y divide-gray-200">
                <tr v-for="(returnItem, index) in returns" :key="returnItem.id">
                  <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-500">{{ index + 1 }}</td>
                  <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-500">{{ returnItem.id }}</td>
                  <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-500">{{ returnItem.transactionId }}</td>
                  <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-500">{{ returnItem.customerId }}</td>
                  <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-500">Rp{{ formatCurrency(returnItem.amount) }}</td>
                  <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-500">{{ returnItem.reason }}</td>
                  <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-500">{{ returnItem.returnDate }}</td>
                  <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-500">{{ returnItem.returnTime }}</td>
                  <td class="px-6 py-4 whitespace-nowrap">
                    <span 
                      class="px-2 py-1 inline-flex text-xs leading-5 font-semibold rounded-full"
                      :class="getStatusClass(returnItem.status)"
                    >
                      {{ formatStatus(returnItem.status) }}
                    </span>
                  </td>
                  <td class="px-6 py-4 whitespace-nowrap text-sm font-medium">
                    <div class="flex space-x-2">
                      <button @click="viewReturn(returnItem.id)" class="text-blue-600 hover:text-blue-900">
                        <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="lucide lucide-eye"><path d="M2 12s3-7 10-7 10 7 10 7-3 7-10 7-10-7-10-7Z"/><circle cx="12" cy="12" r="3"/></svg>
                      </button>
                      <button @click="editReturn(returnItem.id)" class="text-yellow-600 hover:text-yellow-900">
                        <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="lucide lucide-pencil"><path d="M17 3a2.85 2.83 0 1 1 4 4L7.5 20.5 2 22l1.5-5.5Z"/><path d="m15 5 4 4"/></svg>
                      </button>
                      <button @click="deleteReturn(returnItem.id)" class="text-red-600 hover:text-red-900">
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
  import { useReturnStore } from '~/store/return'
  import ReturnFilter from '~/components/return/return-filter.vue'
  
  // Router and stores
  const router = useRouter()
  const returnStore = useReturnStore()
  
  // State
  const showFilter = ref(false)
  
  // Computed
  const returns = computed(() => returnStore.returns)
  const pagination = computed(() => returnStore.pagination)
  const filter = computed(() => returnStore.filter)
  const isLoading = computed(() => returnStore.isLoading)
  const error = computed(() => returnStore.error)
  
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
      'rusak': 'bg-red-100 text-red-800',
      'menunggu': 'bg-yellow-100 text-yellow-800',
      'ditolak': 'bg-gray-100 text-gray-800'
    }
    
    return statusClasses[status] || ''
  }
  
  const applyFilter = (newFilter: Partial<ReturnFilter>) => {
    returnStore.setFilter(newFilter)
  }
  
  const resetFilter = () => {
    returnStore.resetFilter()
    showFilter.value = false
  }
  
  const handlePageChange = (page: number) => {
    returnStore.setFilter({ page })
  }
  
  const viewReturn = (id: string) => {
    router.push(`/admin/return/${id}`)
  }
  
  const editReturn = (id: string) => {
    router.push(`/admin/return/${id}/edit`)
  }
  
  const deleteReturn = async (id: string) => {
    if (confirm('Apakah anda yakin ingin menghapus data pengembalian ini?')) {
      try {
        // Implement delete logic here
        await returnStore.loadReturns()
      } catch (error) {
        console.error('Error deleting return:', error)
      }
    }
  }
  
  // Lifecycle
  onMounted(async () => {
    try {
      await returnStore.loadReturns()
    } catch (error) {
      console.error('Error loading returns:', error)
    }
  })
  </script>