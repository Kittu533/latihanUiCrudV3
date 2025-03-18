<template>
  <Card :class="cardClass">
    <CardHeader :class="headerClass">
      <CardTitle :class="titleClass">{{ title }}</CardTitle>
      <CardDescription v-if="description">{{ description }}</CardDescription>
    </CardHeader>
    <CardContent>
      <div class="flex items-center">
        <div v-if="icon" class="mr-4">
          <component :is="icon" :class="iconClass" />
        </div>
        <div>
          <div class="text-2xl font-bold">{{ value }}</div>
          <p v-if="subtext" class="text-xs text-muted-foreground">
            {{ subtext }}
          </p>
        </div>
      </div>
      <slot></slot>
    </CardContent>
    <CardFooter v-if="$slots.footer">
      <slot name="footer"></slot>
    </CardFooter>
  </Card>
</template>

<script setup lang="ts">
import {
  Card,
  CardHeader,
  CardTitle,
  CardDescription,
  CardContent,
  CardFooter,
} from "@/components/ui/card";

// Definisikan props dengan TypeScript
interface Props {
  title: string;
  description?: string;
  value: string | number;
  subtext?: string;
  icon?: object | null;
  cardClass?: string;
  headerClass?: string;
  titleClass?: string;
  iconClass?: string;
}

// Definisikan props dengan nilai default menggunakan `withDefaults`
const props = withDefaults(defineProps<Props>(), {
  description: "",
  subtext: "",
  icon: null,
  cardClass: "",
  headerClass: "",
  titleClass: "",
  iconClass: "h-8 w-8 text-primary",
});
</script>
