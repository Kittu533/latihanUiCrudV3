<template>
  <div class="min-h-screen bg-white mx-auto px-4">
    <section id="home" class="w-full h-[800px]">
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
    <section id="howitwork" class="w-full h-[800px]">
      <div
        class="container mx-auto px-4 py-4 h-full grid grid-cols-8 gap-4 items-center"
      >
        <div
          class="col-span-8 flex flex-col justify-center items-center gap-24"
        >
          <div
            class="col-span-8 flex flex-col justify-center items-center gap-16"
          >
            <div
              class="col-span-3 flex flex-col flex-1 mx-auto flex-wrap gap-8"
            >
              <Button
                class="bg-blue-300 text-blue-700 hover:bg-blue-300 mx-auto"
                >HOW IT WORK</Button
              >
              <h2 class="text-[38px] items-center text-center">
                Rent with following 3 working steps
              </h2>
            </div>
            <div class="col-span-8 flex justify-center gap-6 w-full max-w-6xl">
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
          <div
            class="col-span-8 flex justify-center gap-12 w-full max-w-6xl items-center"
          >
            <img src="../public/jaguar-logo.png" alt="" />
            <img src="../public/jaguar-logo.png" alt="" />
            <img src="../public/jaguar-logo.png" alt="" />
            <img src="../public/jaguar-logo.png" alt="" />
            <img src="../public/jaguar-logo.png" alt="" />
          </div>
        </div>
      </div>
    </section>
    <section
      id="whychooseus"
      class="w-full h-[800px] "
    >
      <div class="container mx-auto px-4 py-4">
        <div class="grid md:grid-cols-2 gap-16 items-center">
          <!-- LEFT SIDE (Gambar Mobil) -->
          <div class="flex justify-center">
            <img
              class="-scale-x-100 max-w-full"
              src="../public/car-home.webp"
              alt="Car"
            />
          </div>

          <!-- RIGHT SIDE (Konten Text & Card) -->
          <div class="flex flex-col gap-6 w-full">
            <Button class="self-start">WHY CHOOSE US</Button>
            <h2 class="text-[32px] md:text-[38px] font-semibold leading-snug">
              We offer the best experience with our rental deals
            </h2>

            <!-- Card Items -->
            <div class="flex flex-col gap-4">
              <CardHome
                icon="mdi:cash-refund"
                title="Best price guaranteed"
                description="Find a lower price? We'll refund you 100% of the difference."
                :horizontal="true"
              />
              <CardHome
                icon="mdi:shield-check-outline"
                title="Free cancellation"
                description="Cancel your reservation up to 48 hours for free."
                :horizontal="true"
              />
              <CardHome
                icon="mdi:credit-card-outline"
                title="Secure payment"
                description="We offer secure payment options for your convenience."
                :horizontal="true"
              />
              <CardHome
                icon="mdi:map-marker-check"
                title="Top rated locations"
                description="Find the best car rental locations globally."
                :horizontal="true"
              />
            </div>
          </div>
        </div>
      </div>
    </section>
    <section id="popularrental"  class="w-full h-[800px]">
     <div class="container mx-auto px-4 py-4">
      <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-4 gap-6">
        <Card
          v-for="car in cars"
          :key="car.name"
          class="p-6 flex flex-col items-center text-center space-y-4"
        >
          <img
            :src="car.image"
            :alt="car.name"
            class="w-full h-auto object-contain"
          />

          <CardHeader class="space-y-1">
            <CardTitle class="text-lg font-semibold">{{ car.name }}</CardTitle>
            <CardDescription class="text-sm text-gray-600">
              <span class="text-yellow-500 font-semibold"
                >★ {{ car.rating }}</span
              >
              <span class="text-gray-400"> ({{ car.reviews }} reviews)</span>
            </CardDescription>
          </CardHeader>

          <CardContent
            class="grid grid-cols-2 gap-2 text-sm text-gray-600 w-full"
          >
            <div class="flex items-center gap-2">
              <Icon name="mdi:account-multiple-outline" class="w-4 h-4" />
              {{ car.passengers }} Passagers
            </div>
            <div class="flex items-center gap-2">
              <Icon name="mdi:steering" class="w-4 h-4" />
              {{ car.transmission }}
            </div>
            <div class="flex items-center gap-2">
              <Icon name="mdi:snowflake" class="w-4 h-4" />
              Air Conditioning
            </div>
            <div class="flex items-center gap-2">
              <Icon name="mdi:car-door" class="w-4 h-4" />
              {{ car.doors }} Doors
            </div>
          </CardContent>

          <CardFooter class="w-full border-t border-gray-200 pt-4">
            <div class="flex flex-col items-center w-full">
              <p class="text-sm text-gray-400">Price</p>
              <p class="text-xl font-semibold text-gray-900">
                ${{ car.price }} <span class="text-sm font-normal">/day</span>
              </p>
              <Button
                class="mt-4 bg-blue-600 hover:bg-blue-700 text-white text-sm font-medium px-6 py-2 rounded-xl w-full flex justify-center items-center gap-2"
              >
                Rent Now <Icon name="mdi:arrow-right" class="w-4 h-4" />
              </Button>
            </div>
          </CardFooter>
        </Card>
      </div>
     </div>
    </section>
  </div>
</template>

<script setup lang="ts">
import CardHome from "~/components/cardHome/card-home.vue";
import {
  Card,
  CardHeader,
  CardContent,
  CardFooter,
  CardTitle,
  CardDescription,
} from "@/components/ui/card";
import { Button } from "@/components/ui/button";

const cars = [
  {
    name: "Jaguar XE L P250",
    rating: 4.8,
    reviews: "2.436",
    passengers: 4,
    transmission: "Auto",
    doors: 4,
    price: "1,800",
    image: "/Audi.png",
  },
  {
    name: "Audi R8",
    rating: 4.6,
    reviews: "1.936",
    passengers: 2,
    transmission: "Auto",
    doors: 2,
    price: "2,100",
    image: "/Audi.png",
  },
  {
    name: "BMW M3",
    rating: 4.5,
    reviews: "2.036",
    passengers: 4,
    transmission: "Auto",
    doors: 4,
    price: "1,600",
    image: "/Audi.png",
  },
  {
    name: "Lamborghini Huracan",
    rating: 4.3,
    reviews: "2.236",
    passengers: 2,
    transmission: "Auto",
    doors: 2,
    price: "2,300",
    image: "/Audi.png",
  },
];
</script>

<style></style>
