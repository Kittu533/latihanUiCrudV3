<template>
  <div>
    <h2 class="mb-4 text-2xl font-bold">Rental Management</h2>

    <DataTable
      :columns="rentalColumns"
      :data="rentals"
      title="Rental Records"
      itemName="Rental"
      @add="openAddModal"
      @edit="openEditModal"
      @delete="deleteRental"
    />

    <FormModal
      :show="isModalOpen"
      :title="editingRental ? 'Edit Rental' : 'Add New Rental'"
      :fields="rentalFormFields"
      :initial-data="formData"
      @close="isModalOpen = false"
      @save="saveRental"
    />
  </div>
</template>

<script setup lang="ts">
import { ref } from "vue";
import DataTable from "../dashboard/data-table.vue";
import FormModal from "../dashboard/form-modal.vue";
import { useToast } from "../../composables/use-toast";

interface Rental {
  id: string;
  user: string;
  wheelchair: string;
  startDate: string;
  endDate: string;
  status: string;
}

interface FormData {
  user?: string;
  wheelchair?: string;
  startDate?: string;
  endDate?: string;
  status?: string;
}

const { showToast } = useToast();

const rentals = ref<Rental[]>([
  {
    id: "R001",
    user: "Jane Smith",
    wheelchair: "WC002",
    startDate: "2023-04-01",
    endDate: "2023-05-01",
    status: "Active",
  },
  {
    id: "R002",
    user: "Michael Wilson",
    wheelchair: "WC005",
    startDate: "2023-03-15",
    endDate: "2023-04-15",
    status: "Expired",
  },
  {
    id: "R003",
    user: "Emily Davis",
    wheelchair: "WC003",
    startDate: "2023-04-10",
    endDate: "2023-05-10",
    status: "Active",
  },
  {
    id: "R004",
    user: "Robert Johnson",
    wheelchair: "WC001",
    startDate: "2023-02-20",
    endDate: "2023-03-20",
    status: "Returned",
  },
  {
    id: "R005",
    user: "John Doe",
    wheelchair: "WC004",
    startDate: "2023-04-05",
    endDate: "2023-05-05",
    status: "Active",
  },
]);

const users = [
  "John Doe",
  "Jane Smith",
  "Robert Johnson",
  "Emily Davis",
  "Michael Wilson",
];

const wheelchairs = ["WC001", "WC002", "WC003", "WC004", "WC005"];

const isModalOpen = ref(false);
const editingRental = ref<Rental | null>(null);
const formData = ref<FormData>({});

const rentalFormFields = [
  { name: "user", label: "User", type: "select", options: users },
  {
    name: "wheelchair",
    label: "Wheelchair",
    type: "select",
    options: wheelchairs,
  },
  { name: "startDate", label: "Start Date", type: "text" },
  { name: "endDate", label: "End Date", type: "text" },
  {
    name: "status",
    label: "Status",
    type: "select",
    options: ["Active", "Expired", "Returned"],
  },
];

const openAddModal = () => {
  editingRental.value = null;
  formData.value = {};
  isModalOpen.value = true;
};

const openEditModal = (item: DataItem) => {
  const rental = item as Rental;
  editingRental.value = rental;
  formData.value = { ...rental };
  isModalOpen.value = true;
};

const saveRental = (data: FormData) => {
  if (editingRental.value) {
    // Update existing rental
    const index = rentals.value.findIndex(
      (r) => r.id === editingRental.value!.id
    );
    if (index !== -1) {
      rentals.value[index] = { ...rentals.value[index], ...data };
    }
  } else {
    // Add new rental
    const newId = `R${String(rentals.value.length + 1).padStart(3, "0")}`;
    rentals.value.push({ id: newId, ...data } as Rental);
  }

  isModalOpen.value = false;
  showToast({
    type: "success",
    title: editingRental.value ? "Rental Updated" : "Rental Created",
    message: `Rental has been ${
      editingRental.value ? "updated" : "created"
    } successfully.`,
  });
};

const deleteRental = (item: DataItem) => {
  const rental = item as Rental;
  showToast({
    type: "success",
    title: "Rental Deleted",
    message: "Rental has been deleted successfully.",
  });
};
</script>
