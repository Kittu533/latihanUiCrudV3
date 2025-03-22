<template>
    <div class="bg-white border rounded-md p-4 mb-4">
      <div class="flex justify-between items-center mb-4">
        <h3 class="font-medium">Filter Data</h3>
        <button @click="$emit('close')" class="text-gray-500 hover:text-gray-700">
          <svg xmlns="http://www.w3.org/2000/svg" width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="lucide lucide-x"><path d="M18 6 6 18"/><path d="m6 6 12 12"/></svg>
        </button>
      </div>
      
      <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
        <div>
          <label class="block text-sm font-medium text-gray-700 mb-1">Periode Tanggal</label>
          <div class="relative">
            <input 
              type="date" 
              v-model="localFilter.startDate"
              class="w-full border rounded-md px-3 py-2 focus:outline-none focus:ring-2 focus:ring-blue-500"
            />
            <div class="absolute inset-y-0 right-0 flex items-center pr-3 pointer-events-none">
              <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="lucide lucide-calendar"><rect width="18" height="18" x="3" y="4" rx="2" ry="2"/><line x1="16" x2="16" y1="2" y2="6"/><line x1="8" x2="8" y1="2" y2="6"/><line x1="3" x2="21" y1="10" y2="10"/></svg>
            </div>
          </div>
        </div>
        
        <div>
          <label class="block text-sm font-medium text-gray-700 mb-1">Pilih Status</label>
          <div class="relative">
            <select 
              v-model="localFilter.status"
              class="w-full border rounded-md px-3 py-2 appearance-none focus:outline-none focus:ring-2 focus:ring-blue-500"
            >
              <option value="">Semua Status</option>
              <option value="berhasil">Berhasil</option>
              <option value="rusak">Rusak</option>
              <option value="menunggu">Menunggu</option>
            </select>
            <div class="absolute inset-y-0 right-0 flex items-center pr-3 pointer-events-none">
              <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="lucide lucide-chevron-down"><path d="m6 9 6 6 6-6"/></svg>
            </div>
          </div>
        </div>
      </div>
      
      <div class="flex justify-end mt-4 gap-2">
        <button 
          @click="resetFilters"
          class="px-4 py-2 border rounded-md text-gray-700 hover:bg-gray-50"
        >
          Reset
        </button>
        <button 
          @click="applyFilters"
          class="px-4 py-2 bg-[#4072EE] text-white rounded-md hover:bg-[#3060DD]"
        >
          Terapkan
        </button>
      </div>
    </div>
  </template>
  
  <script setup lang="ts">
  import { ref, reactive, watch } from 'vue'
  import type { ReportFilter } from '~/types/report'
  
  const props = defineProps<{
    filter: ReportFilter
  }>()
  
  const emit = defineEmits<{
    (e: 'apply', filter: Partial<ReportFilter>): void
    (e: 'reset'): void
    (e: 'close'): void
  }>()
  
  const localFilter = reactive<Partial<ReportFilter>>({
    status: props.filter.status || '',
    startDate: props.filter.startDate || '',
    endDate: props.filter.endDate || '',
    search: props.filter.search || ''
  })
  
  // Watch for changes in props
  watch(() => props.filter, (newFilter) => {
    localFilter.status = newFilter.status || ''
    localFilter.startDate = newFilter.startDate || ''
    localFilter.endDate = newFilter.endDate || ''
    localFilter.search = newFilter.search || ''
  }, { deep: true })
  
  const applyFilters = () => {
    emit('apply', { ...localFilter })
  }
  
  const resetFilters = () => {
    localFilter.status = ''
    localFilter.startDate = ''
    localFilter.endDate = ''
    localFilter.search = ''
    emit('reset')
  }
  </script>