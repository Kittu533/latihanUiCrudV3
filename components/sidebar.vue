<template>
  <div
    class="sidebar border-r bg-card text-card-foreground transition-all duration-300"
    :class="{ 'w-64': isOpen, 'w-16': !isOpen }"
  >
    <!-- Logo -->
    <div class="flex h-16 items-center border-b px-4">
      <div class="flex items-center gap-2">
        <div
          class="flex h-8 w-8 items-center justify-center rounded-full bg-orange-500 text-white"
        >
          <CircleIcon class="h-4 w-4" />
        </div>
        <span class="font-semibold" v-show="isOpen">Wheel Care</span>
      </div>
    </div>

    <!-- Navigation -->
    <div class="py-4">
      <nav class="space-y-1 px-2">
        
        <SidebarGroup
          v-for="(group, index) in navigation"
          :key="index"
          :title="group.title"
          :is-open="isOpen"
        >
          <SidebarItem class="hover:bg-muted/50 pl-8 " 
            v-for="item in group.items"
            :key="item.name"
            :item="item"
            :is-sidebar-open="isOpen"
            :active="currentPath === item.href"
            @click="navigateTo(item.href)"
          />
        </SidebarGroup>
      </nav>
    </div>
  </div>
</template>
<script setup lang="ts">
import { computed } from "vue";
import { useRoute } from "vue-router";
import {
  CircleIcon,
  HomeIcon,
  UsersIcon,
  PackageIcon,
  ShoppingCartIcon,
  ArrowLeftRightIcon,
  FileTextIcon,
} from "lucide-vue-next";

interface NavigationItem {
  name: string;
  href: string;
  icon: any; // Consider using a more specific type for icons
}

interface NavigationGroup {
  title: string;
  items: NavigationItem[];
}

defineProps<{
  isOpen: boolean;
}>();

const route = useRoute();
const currentPath = computed(() => route.path);

const navigation: NavigationGroup[] = [
  // {
  //   title: "",
  //   items: [{ name: "Beranda", href: "/", icon: HomeIcon }],
  // },
  {
    title: "Pengguna",
    items: [
      { name: "Pengguna", href: "/pengguna", icon: UsersIcon },
      { name: "Pelanggan", href: "/pengguna/pelanggan", icon: UsersIcon },
      { name: "Guide", href: "/pengguna/guide", icon: FileTextIcon },
      { name: "Agen", href: "/pengguna/agen", icon: UsersIcon },
    ],
  },
  {
    title: "Produk & Transaksi",
    items: [
      { name: "Produk", href: "/produk", icon: PackageIcon },
      { name: "Transaksi", href: "/transaksi", icon: ShoppingCartIcon },
      { name: "Pengembalian", href: "/pengembalian", icon: ArrowLeftRightIcon },
    ],
  },
  {
    title: "Laporan",
    items: [{ name: "Laporan", href: "/laporan", icon: FileTextIcon }],
  },
];

const navigateTo = (path: string): void => {
  // In a real Nuxt app, you would use the navigateTo utility
  // This is a placeholder for demonstration
  console.log("Navigate to:", path);
};
</script>
