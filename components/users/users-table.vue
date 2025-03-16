<template>
  <div>
    <h2 class="mb-4 text-2xl font-bold">Users Management</h2>

    <DataTable
      title="Users List"
      item-name="User"
      :columns="['ID', 'Name', 'Email', 'Role', 'Status', 'Last Login']"
      :data="users"
      @add="openAddModal"
      @edit="openEditModal"
      @delete="deleteUser"
    />

    <FormModal
      :show="isModalOpen"
      :title="editingUser ? 'Edit User' : 'Add New User'"
      :fields="userFormFields"
      :initial-data="formData"
      @close="isModalOpen = false"
      @save="saveUser"
    />
  </div>
</template>

<script setup lang="ts">
import { ref } from 'vue';
import DataTable from '../dashboard/data-table.vue';
import FormModal from '../dashboard/form-modal.vue';
import { useToast } from '../../composables/use-toast';

interface User {
  id: number;
  name: string;
  email: string;
  role: string;
  status: string;
  lastLogin: string;
}

interface FormData {
  name?: string;
  email?: string;
  role?: string;
  status?: string;
}

const { showToast } = useToast();

const users = ref<User[]>([
  { id: 1, name: 'John Doe', email: 'john@example.com', role: 'Admin', status: 'Active', lastLogin: '2023-04-15' },
  { id: 2, name: 'Jane Smith', email: 'jane@example.com', role: 'User', status: 'Active', lastLogin: '2023-04-14' },
  { id: 3, name: 'Robert Johnson', email: 'robert@example.com', role: 'User', status: 'Inactive', lastLogin: '2023-03-28' },
  { id: 4, name: 'Emily Davis', email: 'emily@example.com', role: 'Manager', status: 'Active', lastLogin: '2023-04-12' },
  { id: 5, name: 'Michael Wilson', email: 'michael@example.com', role: 'User', status: 'Active', lastLogin: '2023-04-10' },
]);

const isModalOpen = ref(false);
const editingUser = ref<User | null>(null);
const formData = ref<FormData>({});

const userFormFields = [
  { name: 'name', label: 'Name', type: 'text' },
  { name: 'email', label: 'Email', type: 'email' },
  { name: 'role', label: 'Role', type: 'select', options: ['Admin', 'Manager', 'User'] },
  { name: 'status', label: 'Status', type: 'select', options: ['Active', 'Inactive'] },
];

const openAddModal = () => {
  editingUser.value = null;
  formData.value = {};
  isModalOpen.value = true;
};

const openEditModal = (user: User) => {
  editingUser.value = user;
  formData.value = { ...user };
  isModalOpen.value = true;
};

const saveUser = (data: FormData) => {
  if (editingUser.value) {
    // Update existing user
    const index = users.value.findIndex(u => u.id === editingUser.value!.id);
    if (index !== -1) {
      users.value[index] = { ...users.value[index], ...data };
    }
  } else {
    // Add new user
    const newId = Math.max(...users.value.map(u => u.id)) + 1;
    users.value.push({ id: newId, ...data, lastLogin: 'Never' } as User);
  }

  isModalOpen.value = false;
  showToast({
    type: 'success',
    title: editingUser.value ? 'User Updated' : 'User Created',
    message: `User has been ${editingUser.value ? 'updated' : 'created'} successfully.`,
  });
};

const deleteUser = (user: User) => {
  users.value = users.value.filter(u => u.id !== user.id);

  showToast({
    type: 'success',
    title: 'User Deleted',
    message: 'User has been deleted successfully.',
  });
};
</script>