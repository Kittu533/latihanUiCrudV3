<template>
    <div class="flex h-screen bg-gray-100 dark:bg-gray-900">
      <!-- Sidebar -->
      <AppSidebar
        :is-open="isSidebarOpen"
        @toggle="isSidebarOpen = !isSidebarOpen"
      />
  
      <!-- Main Content -->
      <div class="flex flex-1 flex-col overflow-hidden">
        <!-- Header -->
        <AppHeader @toggle-sidebar="isSidebarOpen = !isSidebarOpen" />
  
        <!-- Main Content Area -->
        <main class="flex-1 overflow-auto p-4">
          <slot></slot>
        </main>
      </div>
  
      <!-- Toast Notification -->
      <ToastNotification
        v-if="toast.show"
        :type="toast.type"
        :title="toast.title"
        :message="toast.message"
        @close="toast.show = false"
      />
    </div>
  </template>
  
  <script setup lang="ts">
  import { ref } from "vue";
  import AppSidebar from "../layouts/app-sidebar.vue";
  import AppHeader from "../layouts/app-header.vue";
  import ToastNotification from "../components/dashboard/toast-notification.vue";
  import { useToast } from "../composables/use-toast";
  
  const isSidebarOpen = ref(false);
  const { toast } = useToast();
  </script>