<!-- components/dashboard/Sidebar.vue -->
<template>
  <!-- Sidebar -->
  <aside
    :class="[
      'fixed inset-y-0 z-50 flex w-72 flex-col border-r bg-white transition-all dark:border-gray-800 dark:bg-gray-950 lg:static',
      isSidebarOpen ? 'left-0' : '-left-72 lg:left-0',
    ]"
  >
    <div class="flex h-14 items-center border-b px-4 dark:border-gray-800">
      <h1 class="text-lg font-semibold">Wheelchair Admin</h1>
    </div>
    <nav class="flex-1 overflow-auto py-4">
      <ul class="grid gap-1 px-2">
        <li v-for="(item, index) in navigationItems" :key="index">
          <a
            href="#"
            @click.prevent="activeSection = item.id"
            :class="[
              'flex items-center gap-3 rounded-md px-3 py-2 text-sm font-medium',
              activeSection === item.id
                ? 'bg-gray-100 text-gray-900 dark:bg-gray-800 dark:text-gray-50'
                : 'text-gray-500 hover:bg-gray-100 dark:text-gray-400 dark:hover:bg-gray-800',
            ]"
          >
            <component :is="item.icon" class="h-5 w-5" />
            {{ item.name }}
          </a>
        </li>
      </ul>
    </nav>
    <div class="border-t p-4 dark:border-gray-800">
      <button
        @click="toggleTheme"
        class="flex w-full items-center gap-3 rounded-md px-3 py-2 text-sm font-medium text-gray-500 hover:bg-gray-100 dark:text-gray-400 dark:hover:bg-gray-800"
      >
        <sun-icon v-if="isDarkMode" class="h-5 w-5" />
        <moon-icon v-else class="h-5 w-5" />
        {{ isDarkMode ? "Light Mode" : "Dark Mode" }}
      </button>
      <button
        class="mt-2 flex w-full items-center gap-3 rounded-md px-3 py-2 text-sm font-medium text-gray-500 hover:bg-gray-100 dark:text-gray-400 dark:hover:bg-gray-800"
      >
        <log-out-icon class="h-5 w-5" />
        Logout
      </button>
    </div>
  </aside>
</template>

<script setup lang="ts">
import { ref } from "vue";
import { useRoute } from "#app"; // or 'vue-router' in older versions

// Example icons inline or from 'icons.ts'
const sunIcon = {
  /* ... */
};
const moonIcon = {
  /* ... */
};
const logOutIcon = {
  /* ... */
};

const isDarkMode = ref(false);

const currentRoute = useRoute().name; // misal 'admin-index', 'admin-users', etc.

// Daftar menu
const navigationItems = [
  { id: "", name: "Dashboard", icon: sunIcon },
  { id: "users", name: "Users Management", icon: moonIcon },
  { id: "wheelchairs", name: "Wheelchair Management", icon: moonIcon },
  { id: "rentals", name: "Rental Management", icon: moonIcon },
  { id: "settings", name: "Settings", icon: moonIcon },
];
</script>
