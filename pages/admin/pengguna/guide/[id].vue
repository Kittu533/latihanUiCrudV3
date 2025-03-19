<template>
    <div class="space-y-4">
      <div class="flex items-center justify-between">
        <div>
          <h2 class="text-2xl font-bold tracking-tight">Pelanggan - detail</h2>
        </div>
      </div>
  
      <!-- Breadcrumb Navigation -->
      <div class="flex items-center space-x-2 text-sm">
        <NuxtLink to="/" class="text-muted-foreground hover:text-foreground">
          <HomeIcon class="h-4 w-4" />
        </NuxtLink>
        <ChevronRightIcon class="h-4 w-4 text-muted-foreground" />
        <NuxtLink to="/admin/pengguna/pelanggan" class="text-muted-foreground hover:text-foreground">
          Pengguna
        </NuxtLink>
        <ChevronRightIcon class="h-4 w-4 text-muted-foreground" />
        <span>Pelanggan</span>
      </div>
  
      <!-- Customer Data Card -->
      <Card class="w-full">
        <CardHeader>
          <CardTitle>Data Pelanggan</CardTitle>
        </CardHeader>
        <CardContent>
          <div class="rounded-md border"> 
            <table class="w-full">
              <tbody>
                <!-- ID Pelanggan -->
                <tr class="border-b">
                  <td class="w-1/3 px-4 py-3 font-medium text-muted-foreground bg-muted/10">
                    ID Pelanggan
                  </td>
                  <td class="w-2/3 px-4 py-3">
                    {{ customer.id }}
                  </td>
                </tr>
                
                <!-- Nama Lengkap -->
                <tr class="border-b">
                  <td class="w-1/3 px-4 py-3 font-medium text-muted-foreground bg-muted/10">
                    Nama Lengkap
                  </td>
                  <td class="w-2/3 px-4 py-3">
                    {{ customer.name }}
                  </td>
                </tr>
                
                <!-- Nomor Telepon -->
                <tr class="border-b">
                  <td class="w-1/3 px-4 py-3 font-medium text-muted-foreground bg-muted/10">
                    Nomor Telepon
                  </td>
                  <td class="w-2/3 px-4 py-3">
                    {{ customer.phone }}
                  </td>
                </tr>
                
                <!-- Email -->
                <tr class="border-b">
                  <td class="w-1/3 px-4 py-3 font-medium text-muted-foreground bg-muted/10">
                    Email
                  </td>
                  <td class="w-2/3 px-4 py-3">
                    {{ customer.email }}
                  </td>
                </tr>
                
                <!-- Gender -->
                <tr class="border-b">
                  <td class="w-1/3 px-4 py-3 font-medium text-muted-foreground bg-muted/10">
                    Gender
                  </td>
                  <td class="w-2/3 px-4 py-3">
                    {{ formatGender(customer.gender) }}
                  </td>
                </tr>
                
                <!-- Akun Dibuat -->
                <tr class="border-b">
                  <td class="w-1/3 px-4 py-3 font-medium text-muted-foreground bg-muted/10">
                    Akun dibuat
                  </td>
                  <td class="w-2/3 px-4 py-3">
                    {{ customer.registerDate }}
                  </td>
                </tr>
                
                <!-- Akun Diperbarui -->
                <tr class="border-b">
                  <td class="w-1/3 px-4 py-3 font-medium text-muted-foreground bg-muted/10">
                    Akun diperbarui
                  </td>
                  <td class="w-2/3 px-4 py-3">
                    {{ customer.lastLogin }}
                  </td>
                </tr>
                
                <!-- Status -->
                <tr>
                  <td class="w-1/3 px-4 py-3 font-medium text-muted-foreground bg-muted/10">
                    Status
                  </td>
                  <td class="w-2/3 px-4 py-3">
                    <Badge :class="getStatusClass(customer.status)">
                      {{ formatStatus(customer.status) }}
                    </Badge>
                  </td>
                </tr>
              </tbody>
            </table>
          </div>
        </CardContent>
      </Card>
  
      <!-- Action Buttons -->
      <div class="flex justify-end mt-6">
        <Button class="bg-[#4072EE] text-white hover:bg-[#3060DD]" @click="saveCustomer">
          Simpan
        </Button>
      </div>
    </div>
  </template>
  
  <script setup lang="ts">
  import { ref, onMounted } from 'vue';
  import { useRoute, useRouter } from 'vue-router';
  import { 
    HomeIcon, 
    ChevronRightIcon 
  } from 'lucide-vue-next';
  import { 
    Card, 
    CardHeader, 
    CardTitle, 
    CardContent 
  } from '~/components/ui/card';
  import { Button } from '~/components/ui/button';
  import { Badge } from '~/components/ui/badge';
  
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
  
  // State
  const route = useRoute();
  const router = useRouter();
  const customerId = route.params.id as string;
  const customer = ref<Customer>({
    id: 'P0001',
    name: 'Herman Prakoso',
    phone: '+6281234567890',
    email: 'herman123@gmail.com',
    gender: 'laki-laki',
    registerDate: '01/03/2025',
    lastLogin: '12/03/2025',
    status: 'menunggu'
  });
  
  // Methods
  const fetchCustomerData = async () => {
    try {
      // In a real application, you would fetch data from an API
      // For now, we'll use mock data that matches the ID from the route
      
      // This is just a placeholder for the API call
      // const response = await fetch(`/api/customers/${customerId}`);
      // customer.value = await response.json();
      
      // For demo purposes, we'll just keep the mock data
    } catch (error) {
      console.error('Error fetching customer data:', error);
    }
  };
  
  const formatGender = (gender: string): string => {
    return gender === 'laki-laki' ? 'Laki-laki' : 'Perempuan';
  };
  
  const formatStatus = (status: string): string => {
    return status.charAt(0).toUpperCase() + status.slice(1);
  };
  
  const getStatusClass = (status: string): string => {
    const statusClasses: Record<string, string> = {
      menunggu: 'bg-yellow-100 text-yellow-800 hover:bg-yellow-100',
      aktif: 'bg-green-100 text-green-800 hover:bg-green-100',
      dibatalkan: 'bg-red-100 text-red-800 hover:bg-red-100',
      nonaktif: 'bg-gray-100 text-gray-800 hover:bg-gray-100'
    };
    
    return statusClasses[status] || '';
  };
  
  const saveCustomer = () => {
    // In a real application, you would save the customer data to an API
    // For now, we'll just show a console message
    console.log('Customer data saved:', customer.value);
    
    // Navigate back to the customer list
    router.push('/pengguna');
  };
  
  // Lifecycle
  onMounted(() => {
    fetchCustomerData();
  });
  </script>
  
  <style scoped>
  body {
    font-family: 'Poppins', sans-serif;
  }
  </style>