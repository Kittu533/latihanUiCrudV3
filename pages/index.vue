<template>
  <div class="min-h-screen bg-white mx-auto px-4">
    <section id="home" class="w-full">
      <div
        class="container mx-auto px-4 py-4 h-full grid grid-cols-8 gap-4 items-center"
      >
        <div class="col-span-3 max-w-xl">
          <h1 class="text-4xl md:text-5xl font-bold mb-4">
            Find, book and rent a car
            <span class="text-blue-600">Easily</span>
          </h1>
          <p class="text-gray-600 mb-8">
            Get a car wherever and whenever you need it with your iOS and
            Android device.
          </p>
          <div class="flex space-x-4 mb-12">
            <a href="#" class="inline-block">
              <img
                src="../public/goggle-play.webp"
                alt="Get it on Google Play"
                class="h-12"
              />
            </a>
            <a href="#" class="inline-block">
              <img
                src="../public/app-store.webp"
                alt="Download on App Store"
                class="h-12"
              />
            </a>
          </div>
        </div>
        <div class="col-span-5 relative">
          <!-- Foreground Image (Mobil) -->
          <img src="../public/car.png" class="relative z-10 w-full" alt="car" />
        </div>
      </div>
      <div class="container mx-auto px-4 -mt-6 lg:-mt-16 mb-8 relative z-10">
        <div class="p-6 bg-white shadow-lg rounded-lg grid grid-cols-8 gap-4">
          <div class="col-span-2 space-y-2">
            <Label>Location</Label>
            <div class="flex items-center">
              <IconMapPin class="w-4 h-4 text-gray-500 mr-2" />
              <Input placeholder="Search your location" />
            </div>
          </div>
          <div class="col-span-2 space-y-2">
            <Label>Pickup date</Label>
            <div class="flex items-center">
              <IconCalendar class="w-4 h-4 text-gray-500 mr-2" />
              <Input type="datetime-local" />
            </div>
          </div>
          <div class="col-span-2 space-y-2">
            <Label>Return date</Label>
            <div class="flex items-center">
              <IconCalendar class="w-4 h-4 text-gray-500 mr-2" />
              <Input type="datetime-local" />
            </div>
          </div>
          <div class="col-span-2 flex items-end">
            <Button class="w-full bg-blue-600 text-white hover:bg-blue-700"
              >Search</Button
            >
          </div>
        </div>
      </div>
    </section>
    <section id="howitwork" class="w-full">
      <div>
        <div
          class="container mx-auto px-4 py-4 h-full grid grid-cols-8 gap-4 items-center"
        >
          <div class="flex">
            <div class="col-span-3 max-w-xl">
              <Button>HOW IT WORK</Button>
              <div></div>
            </div>
            <div class="flex  space-y-4">
              <CardHome
                icon="mdi:map-marker-outline"
                title="Choose Location"
                description="Choose your location and find your best wheelchair"
              />
              <CardHome
                icon="mdi:calendar-month-outline"
                title="Select Pickup Date"
                description="Pick your desired date to rent a car"
              />
              <CardHome
                icon="mdi:wheelchair-accessibility"
                title="Accessible Vehicle"
                description="Find wheelchair-friendly accessible vehicles"
              />
            </div>
          </div>
          <div>
            <div></div>
          </div>
        </div>
      </div>
    </section>
  </div>
</template>

<script setup lang="ts">
import CardHome from '~/components/cardHome/card-home.vue';
</script>

<style></style>
