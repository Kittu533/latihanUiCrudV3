<template>
  <div>
    <!-- Search Input -->


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
              <template v-if="column.render && column.key !== 'actions'">
                <component
                  :is="column.render(row[column.key]).component"
                  :class="column.render(row[column.key]).class"
                >
                  {{ column.render(row[column.key]).text }}
                </component>
              </template>
              <template v-else-if="column.key === 'actions'">
                <div v-html="column.render().slots.default"></div>
              </template>
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
import { Input } from '@/components/ui/input';

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
  render?: (value: unknown) => ColumnRenderResult;
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

// Methods
const handleSearch = (): void => {
  emit('search', searchQuery.value);
};

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