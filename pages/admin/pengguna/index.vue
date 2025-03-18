<template>
  <div class="space-y-4">
    <div class="flex items-center justify-between">
      <div>
        <h2 class="text-2xl font-bold tracking-tight">Pengguna</h2>
      </div>
      <div class="flex items-center gap-2">
        <Button variant="outline" @click="showFilter = !showFilter">
          <FilterIcon class="mr-2 h-4 w-4" />
          Filter
        </Button>
      </div>
    </div>

    <!-- Filter Panel -->
    <Card v-if="showFilter" class="mb-4">
      <CardHeader>
        <CardTitle>Filter Data</CardTitle>
        <Button
          variant="ghost"
          size="icon"
          class="absolute right-2 top-2"
          @click="showFilter = false"
        >
          <XIcon class="h-4 w-4" />
        </Button>
      </CardHeader>
      <CardContent>
        <form @submit.prevent="applyFilter" class="w-full">
          <div class="grid grid-cols-1 md:grid-cols-3 gap-6">
            <div class="space-y-2">
              <Label for="periode">Periode Tanggal</Label>
              <Input
                id="periode"
                type="date"
                placeholder="Tanggal Awal"
                v-model="filterDate"
                class="w-full"
              />
            </div>
            <div class="space-y-2">
              <Label for="status">Pilih Status</Label>
              <Select v-model="filterStatus" class="w-full">
                <SelectTrigger>
                  <SelectValue placeholder="Pilih status" />
                </SelectTrigger>
                <SelectContent>
                  <SelectItem value="semua">Semua Status</SelectItem>
                  <SelectItem value="menunggu">Menunggu</SelectItem>
                  <SelectItem value="aktif">Aktif</SelectItem>
                  <SelectItem value="dibatalkan">Dibatalkan</SelectItem>
                  <SelectItem value="nonaktif">Nonaktif</SelectItem>
                </SelectContent>
              </Select>
            </div>
            <div
              class="space-y-2 flex mx-auto flex-wrap justify-between items-end gap-2"
            >
              <Button
                class="bg-[#4072EE] w-[115px] h-[34px] text-white hover:bg-[#4072EE]"
                type="submit"
              >
                Terapkan
              </Button>
              <Button
                class="border-red-500 text-red-500 w-[95px] h-[34px] hover:text-red-500"
                type="button"
                variant="outline"
                @click="resetFilter"
              >
                Batal
              </Button>
            </div>
          </div>
        </form>
      </CardContent>
    </Card>

    <!-- Data Table -->
    <Card>
      <CardHeader>
        <CardTitle>Data Pengguna</CardTitle>
      </CardHeader>
      <CardContent>
        <DataTable
          :data="pagination.data"
          :columns="columns"
          :pagination="pagination"
          @page-change="handlePageChange"
        />
      </CardContent>
    </Card>
  </div>
</template>

<script setup lang="ts">
import { ref, computed } from "vue";
import { FilterIcon, PlusIcon, XIcon } from "lucide-vue-next";
import { Button } from "@/components/ui/button";
import { Input } from "@/components/ui/input";
import { Card, CardHeader, CardTitle, CardContent } from "~/components/ui/card";
import { Label } from "@/components/ui/label";
import {
  Select,
  SelectContent,
  SelectItem,
  SelectTrigger,
  SelectValue,
} from "~/components/ui/select";
import DataTable from "~/components/data-table.vue";

// Types
interface Customer {
  id: string;
  name: string;
  phone: string;
  email: string;
  gender: string;
  registerDate: string;
  lastLogin: string;
  status: string;
}

interface Column {
  key: string;
  label: string;
  render?: (value: any) => any;
}

interface Pagination {
  currentPage: number;
  totalPages: number;
  total: number;
  itemsPerPage: number;
  data: Customer[];
}

// State
const showFilter = ref<boolean>(false);
const filterStatus = ref<string>("");
const filterDate = ref<string>("");
const currentPage = ref<number>(1);
const itemsPerPage = ref<number>(5);

const customers = ref<Customer[]>([
  {
    id: "P0001",
    name: "Harman Prakoso",
    phone: "+6281234567890",
    email: "harman123@gmail.com",
    gender: "laki-laki",
    registerDate: "01/03/2025",
    lastLogin: "01/03/2025",
    status: "menunggu",
  },
  {
    id: "P0002",
    name: "Nasisa Fadilla",
    phone: "+6281234567890",
    email: "nasifadilla.26@gmail.com",
    gender: "perempuan",
    registerDate: "02/03/2025",
    lastLogin: "07/03/2025",
    status: "menunggu",
  },
  {
    id: "P0003",
    name: "Mia Anggreni",
    phone: "+6281234567890",
    email: "miaanggreni@gmail.com",
    gender: "perempuan",
    registerDate: "03/03/2025",
    lastLogin: "04/03/2025",
    status: "aktif",
  },
  {
    id: "P0004",
    name: "Finda Alifa",
    phone: "+6281234567890",
    email: "findal123@gmail.com",
    gender: "perempuan",
    registerDate: "04/03/2025",
    lastLogin: "09/03/2025",
    status: "dibatalkan",
  },
  {
    id: "P0005",
    name: "Ilham Dean",
    phone: "+6281234567890",
    email: "ilhamdean0123@gmail.com",
    gender: "laki-laki",
    registerDate: "05/03/2025",
    lastLogin: "08/03/2025",
    status: "nonaktif",
  },
  {
    id: "P0005",
    name: "Ilham Dean",
    phone: "+6281234567890",
    email: "ilhamdean0123@gmail.com",
    gender: "laki-laki",
    registerDate: "05/03/2025",
    lastLogin: "08/03/2025",
    status: "nonaktif",
  }, {
    id: "P0005",
    name: "Ilham Dean",
    phone: "+6281234567890",
    email: "ilhamdean0123@gmail.com",
    gender: "laki-laki",
    registerDate: "05/03/2025",
    lastLogin: "08/03/2025",
    status: "nonaktif",
  }, {
    id: "P0005",
    name: "Ilham Dean",
    phone: "+6281234567890",
    email: "ilhamdean0123@gmail.com",
    gender: "laki-laki",
    registerDate: "05/03/2025",
    lastLogin: "08/03/2025",
    status: "nonaktif",
  }, {
    id: "P0005",
    name: "Ilham Dean",
    phone: "+6281234567890",
    email: "ilhamdean0123@gmail.com",
    gender: "laki-laki",
    registerDate: "05/03/2025",
    lastLogin: "08/03/2025",
    status: "nonaktif",
  },
]);

// Table columns configuration
const columns: Column[] = [
  { key: "id", label: "ID Pelanggan" },
  { key: "name", label: "Nama Lengkap" },
  { key: "phone", label: "Nomor Telepon" },
  { key: "email", label: "Alamat Email" },
  { key: "gender", label: "Jenis Kelamin" },
  { key: "registerDate", label: "Akun Dibuat" },
  { key: "lastLogin", label: "Akun Diperbarui" },
  {
    key: "status",
    label: "Status Akun",
    render: (value: string) => {
      const statusClasses: Record<string, string> = {
        menunggu: "bg-yellow-100 text-yellow-800",
        aktif: "bg-green-100 text-green-800",
        dibatalkan: "bg-red-100 text-red-800",
        nonaktif: "bg-gray-100 text-gray-800",
      };
      return {
        component: "span",
        class: `px-2 py-1 rounded-md text-xs font-medium ${
          statusClasses[value] || ""
        }`,
        text: value.charAt(0).toUpperCase() + value.slice(1),
      };
    },
  },
  {
    key: "actions",
    label: "Aksi",
    render: () => ({
      component: "div",
      class: "flex space-x-2",
      slots: {
        default: `
            <button class="p-1 text-blue-600 hover:text-blue-800">
              <span class="sr-only">View</span>
              <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="lucide lucide-eye"><path d="M2 12s3-7 10-7 10 7 10 7-3 7-10 7-10-7-10-7Z"/><circle cx="12" cy="12" r="3"/></svg>
            </button>
            <button class="p-1 text-yellow-600 hover:text-yellow-800">
              <span class="sr-only">Edit</span>
              <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="lucide lucide-pencil"><path d="M17 3a2.85 2.83 0 1 1 4 4L7.5 20.5 2 22l1.5-5.5Z"/><path d="m15 5 4 4"/></svg>
            </button>
            <button class="p-1 text-red-600 hover:text-red-800">
              <span class="sr-only">Delete</span>
              <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="lucide lucide-trash-2"><path d="M3 6h18"/><path d="M19 6v14c0 1-1 2-2 2H7c-1 0-2-1-2-2V6"/><path d="M8 6V4c0-1 1-2 2-2h4c1 0 2 1 2 2v2"/><line x1="10" x2="10" y1="11" y2="17"/><line x1="14" x2="14" y1="11" y2="17"/></svg>
            </button>
          `,
      },
    }),
  },
  
  
];

// Computed
const filteredCustomers = computed<Customer[]>(() => {
  let filtered = [...customers.value];

  if (filterStatus.value && filterStatus.value !== "semua") {
    filtered = filtered.filter(
      (customer) => customer.status === filterStatus.value
    );
  }

  if (filterDate.value) {
    filtered = filtered.filter(
      (customer) =>
        customer.registerDate === filterDate.value ||
        customer.lastLogin === filterDate.value
    );
  }

  return filtered;
});

const pagination = computed<Pagination>(() => {
  const total = filteredCustomers.value.length;
  const totalPages = Math.ceil(total / itemsPerPage.value);

  const start = (currentPage.value - 1) * itemsPerPage.value;
  const end = start + itemsPerPage.value;
  const paginatedData = filteredCustomers.value.slice(start, end);

  return {
    currentPage: currentPage.value,
    totalPages,
    total,
    itemsPerPage: itemsPerPage.value,
    data: paginatedData,
  };
});

// Methods
const applyFilter = (): void => {
  currentPage.value = 1;
  // Keep the filter panel open to show the applied filters
};

const resetFilter = (): void => {
  filterStatus.value = "";
  filterDate.value = "";
  currentPage.value = 1;
  showFilter.value = false;
};

const handlePageChange = (page: number): void => {
  currentPage.value = page;
};
</script>
<style>

body {
  font-family: 'Poppins', sans-serif;
}
</style>