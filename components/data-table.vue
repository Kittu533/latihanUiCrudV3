<template>
  <div>
    <!-- Table -->
    <div class="rounded-md border">
      <table class="w-full text-sm">
        <thead>
          <tr class="border-b bg-muted/50 transition-colors">
            <th
              v-for="column in columns"
              :key="column.key"
              class="h-10 px-4 text-left align-middle font-medium text-muted-foreground"
              :class="column.class"
            >
              {{ column.label }}
            </th>
          </tr>
        </thead>
        <tbody>
          <tr
            v-for="(row, rowIndex) in displayData"
            :key="rowIndex"
            class="border-b transition-colors hover:bg-muted/50"
          >
            <td
              v-for="column in columns"
              :key="`${rowIndex}-${column.key}`"
              class="p-4 align-middle"
            >
              <!-- Regular cell content -->
              <template v-if="column.render && column.key !== 'actions'">
                <component
                  :is="column.render(row[column.key], row).component"
                  :class="column.render(row[column.key], row).class"
                >
                  {{ column.render(row[column.key], row).text }}
                </component>
              </template>
              
              <!-- Action buttons -->
              <template v-else-if="column.key === 'actions'">
                <div class="flex space-x-2">
                  <!-- View button -->
                  <button 
                    @click="$emit('action', { type: 'view', row })"
                    class="p-1 text-blue-600 hover:text-blue-800"
                  >
                    <span class="sr-only">View</span>
                    <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="lucide lucide-eye"><path d="M2 12s3-7 10-7 10 7 10 7-3 7-10 7-10-7-10-7Z"/><circle cx="12" cy="12" r="3"/></svg>
                  </button>
                  
                  <!-- Edit button -->
                  <button 
                    @click="$emit('action', { type: 'edit', row })"
                    class="p-1 text-yellow-600 hover:text-yellow-800"
                  >
                    <span class="sr-only">Edit</span>
                    <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="lucide lucide-pencil"><path d="M17 3a2.85 2.83 0 1 1 4 4L7.5 20.5 2 22l1.5-5.5Z"/><path d="m15 5 4 4"/></svg>
                  </button>
                  
                  <!-- Delete button -->
                  <button 
                    @click="$emit('action', { type: 'delete', row })"
                    class="p-1 text-red-600 hover:text-red-800"
                  >
                    <span class="sr-only">Delete</span>
                    <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="lucide lucide-trash-2"><path d="M3 6h18"/><path d="M19 6v14c0 1-1 2-2 2H7c-1 0-2-1-2-2V6"/><path d="M8 6V4c0-1 1-2 2-2h4c1 0 2 1 2 2v2"/><line x1="10" x2="10" y1="11" y2="17"/><line x1="14" x2="14" y1="11" y2="17"/></svg>
                  </button>
                </div>
              </template>
              
              <!-- Default cell content -->
              <template v-else>
                {{ row[column.key] }}
              </template>
            </td>
          </tr>
          <tr v-if="displayData.length === 0">
            <td
              :colspan="columns.length"
              class="p-4 text-center text-muted-foreground"
            >
              Tidak ada data yang ditemukan.
            </td>
          </tr>
        </tbody>
      </table>
    </div>

    <!-- Pagination -->
    <div class="flex items-center justify-between py-4">
      <div class="text-sm text-muted-foreground">
        Halaman {{ pagination.currentPage }} dari
        {{ pagination.totalPages }} (Total: {{ pagination.total }})
      </div>
      <div class="flex items-center space-x-2">
        <Button
          variant="outline"
          size="sm"
          @click="handlePrevPage"
          :disabled="pagination.currentPage <= 1"
        >
          Sebelumnya
        </Button>
        <Button
          v-for="page in paginationPages"
          :key="page"
          variant="outline"
          size="sm"
          :class="{
            'bg-primary text-primary-foreground':
              page === pagination.currentPage,
          }"
          @click="$emit('page-change', page)"
        >
          {{ page }}
        </Button>
        <Button
          variant="outline"
          size="sm"
          @click="handleNextPage"
          :disabled="pagination.currentPage >= pagination.totalPages"
        >
          Selanjutnya
        </Button>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, computed } from 'vue';
import { Button } from '@/components/ui/button';

interface ColumnRenderResult {
  component: string;
  class?: string;
  text?: string;
  slots?: { default: string };
}

interface Column {
  key: string;
  label: string;
  class?: string;
  render?: (value: unknown, row?: unknown) => ColumnRenderResult;
}

interface DataItem {
  [key: string]: unknown;
}

interface Pagination {
  currentPage: number;
  totalPages: number;
  total: number;
  data: DataItem[];
}

const props = defineProps<{
  data: DataItem[];
  columns: Column[];
  pagination: Pagination;
}>();

const emit = defineEmits<{
  (e: 'page-change', page: number): void;
  (e: 'search', query: string): void;
  (e: 'action', payload: { type: string, row: DataItem }): void;
}>();

const searchQuery = ref<string>('');

// Computed
const displayData = computed((): DataItem[] => {
  return props.pagination.data;
});

const paginationPages = computed((): number[] => {
  const { currentPage, totalPages } = props.pagination;
  const pages: number[] = [];

  const maxPages = 5;
  const halfMaxPages = Math.floor(maxPages / 2);

  const startPage = Math.max(currentPage - halfMaxPages, 1);
  const endPage = Math.min(startPage + maxPages - 1, totalPages);

  if (endPage - startPage + 1 < maxPages) {
    const adjustedStartPage = Math.max(endPage - maxPages + 1, 1);
    for (let i = adjustedStartPage; i <= endPage; i++) {
      pages.push(i);
    }
  } else {
    for (let i = startPage; i <= endPage; i++) {
      pages.push(i);
    }
  }

  return pages;
});

const handlePrevPage = (): void => {
  if (props.pagination.currentPage > 1) {
    emit('page-change', props.pagination.currentPage - 1);
  }
};

const handleNextPage = (): void => {
  if (props.pagination.currentPage < props.pagination.totalPages) {
    emit('page-change', props.pagination.currentPage + 1);
  }
};
</script>