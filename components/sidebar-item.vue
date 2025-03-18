<template>
  <a
    href="#"
    @click.prevent="$emit('click')"
    class="group flex items-center rounded-md px-3 py-2 text-sm font-medium transition-colors"
    :class="[
      active
        ? 'bg-accent text-accent-foreground'
        : 'text-muted-foreground hover:bg-accent hover:text-accent-foreground',
    ]"
  >
    <component
      :is="item.icon"
      class="mr-2 h-4 w-4 flex-shrink-0"
      v-if="item.icon"
    />
    <span v-if="isSidebarOpen">{{ item.name }}</span>
  </a>
</template>

<script setup lang="ts">
import type { Component } from 'vue';
import type { LucideIcon } from 'lucide-vue-next';

interface NavigationItem {
  name: string;
  href: string;
  icon?: Component | LucideIcon;
}

const props = withDefaults(defineProps<{
  item: NavigationItem;
  active?: boolean;
  isSidebarOpen?: boolean;
}>(), {
  active: false,
  isSidebarOpen: true
});

defineEmits<{
  (e: 'click'): void;
}>();
</script>