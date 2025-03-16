<template>
  <div :class="{ dark: isDarkMode }">
    <DashboardLayout>
      <component :is="currentComponent" />
    </DashboardLayout>
  </div>
</template>

<script setup>
import { ref, provide, computed } from "vue";
import DashboardLayout from "../components/layout/DashboardLayout.vue";
import DashboardOverview from "../components/dashboard/DashboardOverview.vue";
import UsersTable from "../components/users/UsersTable.vue";
import WheelchairsTable from "../components/wheelchairs/WheelchairsTable.vue";
import RentalsTable from "../components/rentals/RentalsTable.vue";
import { provideToast } from "../composables/useToast";

// Provide toast functionality
provideToast();

// Dark mode state
const isDarkMode = ref(false);

// Active section state
const activeSection = ref("dashboard");
provide("activeSection", activeSection);
provide("setActiveSection", (section) => {
  activeSection.value = section;
});

// Determine which component to render based on active section
const currentComponent = computed(() => {
  switch (activeSection.value) {
    case "dashboard":
      return DashboardOverview;
    case "users":
      return UsersTable;
    case "wheelchairs":
      return WheelchairsTable;
    case "rentals":
      return RentalsTable;
    case "settings":
      // For simplicity, we'll just show the dashboard for settings
      return DashboardOverview;
    default:
      return DashboardOverview;
  }
});
</script>

<style>
:root {
  --primary: 222.2 47.4% 55.1%;
  --primary-foreground: 210 40% 98%;
}

.dark {
  --primary: 217.2 91.2% 59.8%;
  --primary-foreground: 210 40% 98%;
}

.bg-primary {
  background-color: hsl(var(--primary));
}

.text-primary {
  color: hsl(var(--primary));
}

.hover\:bg-primary\/90:hover {
  background-color: hsl(var(--primary) / 0.9);
}

html,
body {
  height: 100%;
  margin: 0;
  padding: 0;
}

#app {
  height: 100%;
}
</style>
