<template>
  <div class="flex h-screen overflow-hidden">
    <!-- Sidebar -->
    <Sidebar :is-open="isSidebarOpen" />

    <!-- Main Content -->
    <div class="flex flex-col flex-1 overflow-hidden">
      <!-- Header -->
      <Header @toggle-sidebar="toggleSidebar" :user="user" />

      <!-- Page Content -->
      <main class="flex-1 overflow-y-auto p-4 md:p-6 bg-gray-50">
        <slot />
      </main>
    </div>
  </div>
</template>

<script setup>
import { ref } from "vue";

const isSidebarOpen = ref(true);
const user = ref({
  name: "Selamat Datang",
  role: "Administrator",
  avatar: "/Audi.webp",
});

const toggleSidebar = () => {
  isSidebarOpen.value = !isSidebarOpen.value;
};
</script>
