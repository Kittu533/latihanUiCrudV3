<template>
  <aside
    :class="[
      'fixed inset-y-0 z-50 flex w-72 flex-col border-r bg-white transition-all dark:border-gray-800 dark:bg-gray-950 lg:static',
      isOpen ? 'left-0' : '-left-72 lg:left-0',
    ]"
  >
    <div class="flex h-14 items-center border-b px-4 dark:border-gray-800">
      <h1 class="text-lg font-semibold">Wheelchair Admin</h1>
    </div>
    <nav class="flex-1 overflow-auto py-4">
      <ul class="grid gap-1 px-2">
        <SidebarNavItem
          v-for="(item, index) in navigationItems"
          :key="index"
          :item="item"
          :active="activeSection === item.id"
          @click="setActiveSection(item.id)"
        />
      </ul>
    </nav>
    <div class="border-t p-4 dark:border-gray-800">
      <button
        @click="toggleTheme"
        class="flex w-full items-center gap-3 rounded-md px-3 py-2 text-sm font-medium text-gray-500 hover:bg-gray-100 dark:text-gray-400 dark:hover:bg-gray-800"
      >
        <SunIcon v-if="isDarkMode" class="h-5 w-5" />
        <MoonIcon v-else class="h-5 w-5" />
        {{ isDarkMode ? "Light Mode" : "Dark Mode" }}
      </button>
      <button
        class="mt-2 flex w-full items-center gap-3 rounded-md px-3 py-2 text-sm font-medium text-gray-500 hover:bg-gray-100 dark:text-gray-400 dark:hover:bg-gray-800"
      >
        <LogOutIcon class="h-5 w-5" />
        Logout
      </button>
    </div>
  </aside>
</template>

<script setup lang="ts">
import { ref, inject } from "vue";
import SidebarNavItem from "./sidebar-navitem.vue";
import { SunIcon, MoonIcon, LogOutIcon } from "../components/icon/index";

interface NavigationItem {
  id: string;
  name: string;
  icon: string;
}

const props = defineProps<{
  isOpen: boolean;
}>();

const emit = defineEmits<{
  (e: "toggle"): void;
}>();

const activeSection = inject<string>("activeSection")!;
const setActiveSection = inject<(id: string) => void>("setActiveSection")!;
const isDarkMode = ref(false);

const navigationItems: NavigationItem[] = [
  { id: "dashboard", name: "Dashboard", icon: "HomeIcon" },
  { id: "users", name: "Users Management", icon: "UsersIcon" },
  { id: "wheelchairs", name: "Wheelchair Management", icon: "WheelchairIcon" },
  { id: "rentals", name: "Rental Management", icon: "ClipboardIcon" },
  { id: "settings", name: "Settings", icon: "SettingsIcon" },
];

const toggleTheme = () => {
  isDarkMode.value = !isDarkMode.value;
  if (isDarkMode.value) {
    document.documentElement.classList.add("dark");
  } else {
    document.documentElement.classList.remove("dark");
  }
};
</script>
