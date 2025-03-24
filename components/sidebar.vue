<template>
  <div
    class="sidebar bg-white border-r text-card-foreground transition-all duration-300 h-screen"
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
    <div class="py-4 overflow-y-auto">
      <nav class="space-y-1 px-2 flex flex-col gap-6">
        <div class="" v-for="(group, index) in navigation" :key="index">
          <h2
            v-if="isOpen"
            class="px-3 text-xs font-semibold text-muted-foreground uppercase tracking-wider"
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
                class="h-5 w-5 flex-shrink-0"
                :class="{ 'mr-3': isOpen }"
                v-if="item.icon"
              />
              <span v-if="isOpen">{{ item.name }}</span>
            </router-link>
          </div>
        </div>
      </nav>
    </div>
    
    <!-- Toggle button (optional) -->
    <div v-if="showToggle" class="border-t py-3 px-4">
      <button 
        @click="$emit('toggle')" 
        class="flex items-center justify-center w-full rounded-md py-2 text-sm text-muted-foreground hover:bg-accent hover:text-accent-foreground"
      >
        <component 
          :is="isOpen ? 'ChevronLeftIcon' : 'ChevronRightIcon'" 
          class="h-5 w-5" 
        />
        <span v-if="isOpen" class="ml-2">Collapse</span>
      </button>
    </div>
  </div>
</template>

<script setup lang="ts">
import { computed } from "vue";
import { useRoute } from "vue-router";
import {
  HomeIcon,
  UsersIcon,
  PackageIcon,
  ShoppingCartIcon,
  ArrowLeftRightIcon,
  FileTextIcon,
  ChevronLeftIcon,
  ChevronRightIcon,
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
  showToggle?: boolean;
}>();

const emit = defineEmits(['toggle']);

const route = useRoute();
const currentPath = computed(() => route.path);

// Helper function to check if a route is active (including child routes)
const isRouteActive = (path: string): boolean => {
  return currentPath.value === path || currentPath.value.startsWith(`${path}/`);
};

// Updated navigation to match the image
const navigation: NavigationGroup[] = [
  {
    title: "Beranda",
    items: [{ name: "Beranda", href: "/admin", icon: HomeIcon }],
  },
  {
    title: "Pengguna",
    items: [
      { name: "Pelanggan", href: "/admin/pengguna/pelanggan", icon: UsersIcon },
      { name: "Guide", href: "/admin/pengguna/guide", icon: FileTextIcon },
      { name: "Agen", href: "/admin/pengguna/agent", icon: UsersIcon },
    ],
  },
  {
    title: "Produk",
    items: [
      { name: "Produk", href: "/admin/product", icon: PackageIcon },
    ],
  },
  {
    title: "Transaksi",
    items: [
      { name: "Pemesanan", href: "/admin/transaction/booking", icon: ShoppingCartIcon },
      { name: "Penarikan", href: "/admin/transaction/withdraw", icon: ArrowLeftRightIcon },
    ],
  },
  {
    title: "Pengembalian",
    items: [{ name: "Pengembalian", href: "/admin/return", icon: ArrowLeftRightIcon }],
  },
  {
    title: "Laporan",
    items: [{ name: "Laporan", href: "/admin/report", icon: FileTextIcon }],
  },
];
</script>

<style scoped>
.sidebar {
  box-shadow: 0 1px 3px 0 rgba(0, 0, 0, 0.1);
}

/* Smooth transitions for all elements */
.sidebar * {
  transition-property: width, opacity, margin, padding;
  transition-duration: 300ms;
  transition-timing-function: ease-in-out;
}
</style>