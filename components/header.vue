<template>
  <header
    class="sticky top-0 z-10 flex h-16 items-center gap-4 border-b bg-background px-4 md:px-6"
  >
    <div class="ml-auto flex items-center gap-4">
      <div class="flex items-center gap-2">
        <img
          :src="user.avatar"
          alt="User avatar"
          class="w-[60px] rounded-full border-gray-200 border "
        />
        <div class="hidden md:block">
          <p class="text-sm font-medium">{{ user.name }}</p>
          <p class="text-xs text-muted-foreground">{{ user.role }}</p>
        </div>
      </div>
    </div>
  </header>
</template>

<script setup lang="ts">
import { MenuIcon } from "lucide-vue-next";

interface User {
  avatar: string;
  name: string;
  role: string;
}

defineProps<{
  user: User;
}>();

defineEmits<{
  (e: "toggle-sidebar"): void;
}>();
</script>
