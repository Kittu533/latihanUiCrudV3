<template>
    <div class="mb-4 border rounded-lg p-4 bg-white shadow-sm">
      <div class="flex justify-between items-center mb-4">
        <h3 class="text-lg font-medium">Filter Data</h3>
        <button
          class="p-1 rounded-full hover:bg-muted"
          @click="$emit('close')"
        >
          <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="lucide lucide-x"><path d="M18 6 6 18"/><path d="m6 6 12 12"/></svg>
          <span class="sr-only">Close</span>
        </button>
      </div>
      
      <form @submit.prevent="applyFilter" class="w-full">
        <div class="grid grid-cols-1 md:grid-cols-3 gap-6">
          <div class="space-y-2">
            <label for="periode" class="text-sm font-medium">Periode Tanggal</label>
            <input
              id="periode"
              type="date"
              placeholder="Tanggal"
              v-model="localFilter.date"
              class="w-full px-3 py-2 border rounded-md"
            />
          </div>
          
          <div class="space-y-2">
            <label for="status" class="text-sm font-medium">Pilih Status</label>
            <select 
              id="status"
              v-model="localFilter.status"
              class="w-full px-3 py-2 border rounded-md"
            >
              <option value="">Status</option>
              <option value="tersedia">Tersedia</option>
              <option value="rusak">Rusak</option>
              <option value="disewa">Disewa</option>
            </select>
          </div>
          
          <div class="space-y-2 flex mx-auto flex-wrap justify-between items-end gap-2">
            <button
              class="bg-[#4072EE] text-white px-4 py-2 rounded-md hover:bg-[#3060DD]"
              type="submit"
            >
              Terapkan
            </button>
            <button
              class="border border-red-500 text-red-500 px-4 py-2 rounded-md hover:bg-red-50"
              type="button"
              @click="$emit('reset')"
            >
              Batal
            </button>
          </div>
        </div>
      </form>
    </div>
    </template>
    
    <script setup lang="ts">
    import { ref, watch } from 'vue'
    import type { ProductFilter } from '~/types/product'
    
    // Props and emits
    const props = defineProps<{
      filter: ProductFilter
    }>()
    
    const emit = defineEmits<{
      (e: 'apply', filter: Partial<ProductFilter>): void
      (e: 'reset'): void
      (e: 'close'): void
    }>()
    
    // Local state
    const localFilter = ref({
      status: props.filter.status || '',
      date: props.filter.date || ''
    })
    
    // Watch for prop changes
    watch(() => props.filter, (newFilter) => {
      localFilter.value = {
        status: newFilter.status || '',
        date: newFilter.date || ''
      }
    }, { deep: true })
    
    // Methods
    const applyFilter = () => {
      emit('apply', {
        status: localFilter.value.status,
        date: localFilter.value.date,
        page: 1 // Reset to first page when applying filter
      })
    }
    </script>