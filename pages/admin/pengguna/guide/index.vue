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
          @action="handleAction"
        />
      </CardContent>
    </Card>
  </div>
</template>

<script setup lang="ts">
import { ref, computed } from "vue";
import { FilterIcon, XIcon } from "lucide-vue-next";
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
import { useRouter } from "vue-router";
import DataTable from "~/components/data-table.vue";

// Router
const router = useRouter();

// Types
// Replace the Customer interface with Guide interface
interface Guide {
  idGuide: string;
  fotoProfil: string;
  namaLengkap: string;
  nomorTelepon: string;
  noKtp: string;
  nomorRekening: string;
  akunDibuat: string;
  statusAkun: string;
}

interface Column {
  key: string;
  label: string;
  render?: (value: any, row?: Guide) => any;
}

interface Pagination {
  currentPage: number;
  totalPages: number;
  total: number;
  itemsPerPage: number;
  data: Guide[];
}
const guides = ref<Guide[]>([
  {
    idGuide: "G0001",
    fotoProfil: "https://i.pravatar.cc/150?img=1",
    namaLengkap: "Harman Prakoso",
    nomorTelepon: "+6281234567890",
    noKtp: "3271046789012345",
    nomorRekening: "1234567890",
    akunDibuat: "01/03/2025",
    statusAkun: "menunggu"
  },
  {
    idGuide: "G0002",
    fotoProfil: "https://i.pravatar.cc/150?img=2",
    namaLengkap: "Nasisa Fadilla",
    nomorTelepon: "+6281234567891",
    noKtp: "3271046789012346",
    nomorRekening: "1234567891",
    akunDibuat: "02/03/2025",
    statusAkun: "aktif"
  },
  {
    idGuide: "G0003",
    fotoProfil: "https://i.pravatar.cc/150?img=3",
    namaLengkap: "Mia Anggreni",
    nomorTelepon: "+6281234567892",
    noKtp: "3271046789012347",
    nomorRekening: "1234567892",
    akunDibuat: "03/03/2025",
    statusAkun: "nonaktif"
  },
  {
    idGuide: "G0004",
    fotoProfil: "https://i.pravatar.cc/150?img=4",
    namaLengkap: "Finda Alifa",
    nomorTelepon: "+6281234567893",
    noKtp: "3271046789012348",
    nomorRekening: "1234567893",
    akunDibuat: "04/03/2025",
    statusAkun: "dibatalkan"
  },
  {
    idGuide: "G0005",
    fotoProfil: "https://i.pravatar.cc/150?img=5",
    namaLengkap: "Ilham Dean",
    nomorTelepon: "+6281234567894",
    noKtp: "3271046789012349",
    nomorRekening: "1234567894",
    akunDibuat: "05/03/2025",
    statusAkun: "aktif"
  }
]);
// State
const showFilter = ref<boolean>(false);
const filterStatus = ref<string>("");
const filterDate = ref<string>("");
const currentPage = ref<number>(1);
const itemsPerPage = ref<number>(5);



// Table columns configuration
const columns: Column[] = [
  { key: "idGuide", label: "ID Guide" },
  { 
    key: "fotoProfil", 
    label: "Foto Profil",
    render: (value: string) => ({
      component: "img",
      class: "w-10 h-10 rounded-full",
      src: value,
      alt: "Foto Profil"
    })
  },
  { key: "namaLengkap", label: "Nama Lengkap" },
  { key: "nomorTelepon", label: "Nomor Telepon" },
  { key: "noKtp", label: "No. KTP" },
  { key: "nomorRekening", label: "Nomor Rekening" },
  { key: "akunDibuat", label: "Akun Dibuat" },
  {
    key: "statusAkun",
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
        class: `px-2 py-1 rounded-md text-xs font-medium ${statusClasses[value] || ""}`,
        text: value.charAt(0).toUpperCase() + value.slice(1)
      };
    }
  },
  {
    key: "actions",
    label: "Aksi",
    render: (_, row) => ({
      component: "div",
      class: "flex space-x-2",
      slots: {
        default: `
          <button 
            @click="$emit('action', { type: 'view', guide: ${JSON.stringify(row)} })"
            class="p-1 text-blue-600 hover:text-blue-800"
          >
            <span class="sr-only">View</span>
            <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="lucide lucide-eye"><path d="M2 12s3-7 10-7 10 7 10 7-3 7-10 7-10-7-10-7Z"/><circle cx="12" cy="12" r="3"/></svg>
          </button>
          <button 
            @click="$emit('action', { type: 'edit', guide: ${JSON.stringify(row)} })"
            class="p-1 text-yellow-600 hover:text-yellow-800"
          >
            <span class="sr-only">Edit</span>
            <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="lucide lucide-pencil"><path d="M17 3a2.85 2.83 0 1 1 4 4L7.5 20.5 2 22l1.5-5.5Z"/><path d="m15 5 4 4"/></svg>
          </button>
          <button 
            @click="$emit('action', { type: 'delete', guide: ${JSON.stringify(row)} })"
            class="p-1 text-red-600 hover:text-red-800"
          >
            <span class="sr-only">Delete</span>
            <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="lucide lucide-trash-2"><path d="M3 6h18"/><path d="M19 6v14c0 1-1 2-2 2H7c-1 0-2-1-2-2V6"/><path d="M8 6V4c0-1 1-2 2-2h4c1 0 2 1 2 2v2"/><line x1="10" x2="10" y1="11" y2="17"/><line x1="14" x2="14" y1="11" y2="17"/></svg>
          </button>
        `
      }
    })
  }
];

// Helper functions
const formatStatus = (status: string): string => {
  return status.charAt(0).toUpperCase() + status.slice(1);
};

const getStatusClass = (status: string): string => {
  const statusClasses: Record<string, string> = {
    menunggu: "bg-yellow-100 text-yellow-800",
    aktif: "bg-green-100 text-green-800",
    dibatalkan: "bg-red-100 text-red-800",
    nonaktif: "bg-gray-100 text-gray-800",
  };
  return statusClasses[status] || "";
};

// Action handlers
const viewCustomer = async (guides: Guide): Promise<void> => {
  try {
    console.log("Viewing customer details:", guides);
    await router.push(`/admin/pengguna/guide/${guides.idGuide}`);
  } catch (error) {
    console.error("Error viewing customer details:", error);
    alert("Gagal membuka detail pengguna");
  }
};

// const editCustomer = async (customer: Customer): Promise<void> => {
//   try {
//     console.log("Editing customer:", customer);
//     await router.push(`/admin/pengguna/${customer.id}/edit`);
//   } catch (error) {
//     console.error("Error navigating to edit page:", error);
//     alert("Gagal membuka halaman edit pengguna");
//   }
// };

// const deleteCustomer = async (customer: Customer): Promise<void> => {
//   try {
//     if (!confirm(`Apakah anda yakin ingin menghapus ${customer.name}?`)) {
//       return;
//     }

//     console.log("Deleting customer:", customer);
//     // In a real application, you would call an API
//     // await deleteCustomerAPI(customer.id);

//     customers.value = customers.value.filter((c) => c.id !== customer.id);
//     alert("Pengguna berhasil dihapus");
//   } catch (error) {
//     console.error("Error deleting customer:", error);
//     alert("Gagal menghapus pengguna");
//   }
// };

// Handle action from DataTable
const handleAction = ({ type, row }: { type: string, row: Guide }) => {
  const gui = row;
  switch (type) {
    case 'view':
      viewCustomer(guides);
      break;
  }
};

// Computed
// const filteredCustomers = computed<Customer[]>(() => {
//   let filtered = [...customers.value];

//   if (filterStatus.value && filterStatus.value !== "semua") {
//     filtered = filtered.filter(
//       (customer) => customer.status === filterStatus.value
//     );
//   }

//   if (filterDate.value) {
//     filtered = filtered.filter(
//       (customer) =>
//         customer.registerDate === filterDate.value ||
//         customer.lastLogin === filterDate.value
//     );
//   }

//   return filtered;
// });

// const pagination = computed<Pagination>(() => {
//   const total = filteredCustomers.value.length;
//   const totalPages = Math.ceil(total / itemsPerPage.value);

//   const start = (currentPage.value - 1) * itemsPerPage.value;
//   const end = start + itemsPerPage.value;
//   const paginatedData = filteredCustomers.value.slice(start, end);

//   return {
//     currentPage: currentPage.value,
//     totalPages,
//     total,
//     itemsPerPage: itemsPerPage.value,
//     data: paginatedData,
//   };
// });

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
  font-family: "Poppins", sans-serif;
}
</style>