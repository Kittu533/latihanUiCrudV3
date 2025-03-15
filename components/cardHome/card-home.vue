<!-- components/CardHome.vue -->
<template> 
  <div class="flex flex-col items-center flex-wrap">
    <div class="flex flex-col justify-center items-center px-12 p-4 rounded-lg">
      <div class="text-blue-500 bg-blue-200 p-8 rounded-xl mb-4">
        <BaseIcon :name="icon" />
      </div>
      <h3 class="font-semibold text-[20px] text-center mb-2">{{ title }}</h3>
      <p class="text-[14px] text-gray-500 text-center">
        {{ description }}
      </p>
    </div>
  </div>
</template>

<script setup lang="ts">
import BaseIcon from "../base-icon.vue";

defineProps<{
  icon: string;
  title: string;
  description: string;
}>();
</script>
