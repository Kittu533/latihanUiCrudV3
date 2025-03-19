<template>
  <div v-if="totalPages > 1" class="flex justify-between items-center mt-4">
    <div class="text-sm text-muted-foreground">
      Showing {{ startItem }} to {{ endItem }} of {{ total }} entries
    </div>

    <div class="flex space-x-1">
      <button
        class="px-3 py-1 rounded-md text-sm border hover:bg-muted disabled:opacity-50 disabled:cursor-not-allowed"
        :disabled="currentPage === 1"
        @click="$emit('page-change', currentPage - 1)"
      >
        Previous
      </button>

      <button
        v-for="page in paginationRange"
        :key="page"
        class="px-3 py-1 rounded-md text-sm border hover:bg-muted"
        :class="{ 'bg-primary text-primary-foreground': page === currentPage }"
        :disabled="page === '...'"
        @click="page !== '...' && $emit('page-change', Number(page))"
      >
        {{ page }}
      </button>

      <button
        class="px-3 py-1 rounded-md text-sm border hover:bg-muted disabled:opacity-50 disabled:cursor-not-allowed"
        :disabled="currentPage === totalPages"
        @click="$emit('page-change', currentPage + 1)"
      >
        Next
      </button>
    </div>
  </div>
</template>

<script setup lang="ts">
import { computed } from "vue";
import { getPaginationRange } from "~/utils/helpers";

// Props
const props = defineProps<{
  currentPage: number;
  totalPages: number;
  total: number;
  itemsPerPage: number;
}>();

// Emits
defineEmits<{
  (e: "page-change", page: number): void;
}>();

// Computed
const startItem = computed(
  () => (props.currentPage - 1) * props.itemsPerPage + 1
);

const endItem = computed(() =>
  Math.min(props.currentPage * props.itemsPerPage, props.total)
);

const paginationRange = computed(() =>
  getPaginationRange(props.currentPage, props.totalPages)
);
</script>
