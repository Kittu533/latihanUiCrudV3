<template>
  <div
    class="sidebar bg-card text-card-foreground transition-all duration-300"
    :class="{ 'w-64': isOpen, 'w-16': !isOpen }"
  >
    <!-- Logo -->
    <div class="flex h-16 items-center border-b px-4">
      <div class="flex items-center gap-2">
        <div
          class="flex h-8 w-8 items-center justify-center rounded-full bg-orange-500 text-white"
        >
          <img src="/logo-monitoring.webp" class="w-8 h-8" alt="Logo" />
        </div>
        <span class="font-semibold" v-show="isOpen">Wheel Care</span>
      </div>
    </div>

    <!-- Navigation -->
    <div class="py-4">
      <nav class="space-y-1 px-2">
        <h1
          class="px-3 text-xs font-semibold text-muted-foreground uppercase tracking-wider"
        >
          Beranda
        </h1>
        <div v-for="(group, index) in navigation" :key="index">
          <h2
            v-if="isOpen"
            class="mt-4 px-3 text-xs font-semibold text-muted-foreground uppercase tracking-wider"
          >
            {{ group.title }}
          </h2>
          <div class="mt-1 space-y-1">
            <router-link
              v-for="item in group.items"
              :key="item.name"
              :to="item.href"
              class="group flex items-center rounded-md px-3 py-2 text-sm font-medium transition-colors"
              :class="[
                isRouteActive(item.href)
                  ? 'bg-accent text-accent-foreground font-semibold'
                  : 'text-muted-foreground hover:bg-accent hover:text-accent-foreground',
              ]"
            >
              <component
                :is="item.icon"
                class="mr-2 h-4 w-4 flex-shrink-0"
                v-if="item.icon"
              />
              <span v-if="isOpen">{{ item.name }}</span>
            </router-link>
          </div>
        </div>
      </nav>
    </div>
  </div>
</template>

<script setup lang="ts">
import { computed } from "vue";
import { useRoute } from "vue-router";
import {
  UsersIcon,
  PackageIcon,
  ShoppingCartIcon,
  ArrowLeftRightIcon,
  FileTextIcon,
} from "lucide-vue-next";

interface NavigationItem {
  name: string;
  href: string;
  icon?: any; // Optional icon property
}

interface NavigationGroup {
  title: string;
  items: NavigationItem[];
}

const props = defineProps<{
  isOpen: boolean;
}>();

const route = useRoute();
const currentPath = computed(() => route.path);

// Helper function to check if a route is active (including child routes)
const isRouteActive = (path: string): boolean => {
  return currentPath.value === path || currentPath.value.startsWith(`${path}/`);
};

const navigation: NavigationGroup[] = [
  {
    title: "Pengguna",
    items: [
      { name: "Customer", href: "/admin/pengguna/pelanggan", icon: UsersIcon },
      { name: "Guide", href: "/admin/pengguna/guide", icon: FileTextIcon },
      { name: "Agent", href: "/admin/pengguna/agent", icon: UsersIcon },
    ],
  },
  {
    title: "Produk & Transaksi",
    items: [
      { name: "Produk", href: "/admin/produk", icon: PackageIcon },
      { name: "Transaksi", href: "/admin/transaksi", icon: ShoppingCartIcon },
      {
        name: "Pengembalian",
        href: "/admin/pengembalian",
        icon: ArrowLeftRightIcon,
      },
    ],
  },
  {
    title: "Laporan",
    items: [{ name: "Laporan", href: "/admin/laporan", icon: FileTextIcon }],
  },
];
</script>
