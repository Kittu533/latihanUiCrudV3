<template>
  <li>
    <a
      href="#"
      @click.prevent="$emit('click')"
      :class="[
        'flex items-center gap-3 rounded-md px-3 py-2 text-sm font-medium',
        active
          ? 'bg-gray-100 text-gray-900 dark:bg-gray-800 dark:text-gray-50'
          : 'text-gray-500 hover:bg-gray-100 dark:text-gray-400 dark:hover:bg-gray-800',
      ]"
    >
      <component :is="resolveIcon(item.icon)" class="h-5 w-5" />
      {{ item.name }}
    </a>
  </li>
</template>

<script setup lang="ts">
import { computed } from "vue";
import * as icons from "../components/icon/index";

interface NavigationItem {
  id: string;
  name: string;
  icon: string;
}

const props = defineProps<{
  item: NavigationItem;
  active: boolean;
}>();

const emit = defineEmits<{
  (e: "click"): void;
}>();

const resolveIcon = (iconName: string) => {
  return icons[iconName] || icons.HomeIcon;
};
</script>
