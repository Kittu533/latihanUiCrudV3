<template>
  <div class="min-h-screen flex flex-col mx-auto">
    <!-- Header -->
    <header
      class="container mx-auto px-4 py-4 flex items-center justify-between"
    >
      <div class="flex items-center">
        <img src="/Logo.webp" alt="image" width="100" height="100" />
      </div>
      <nav class="hidden md:flex items-center space-x-6">
        <a href="#" class="text-gray-600 hover:text-gray-900"
          >Become a renter</a
        >
        <a href="#" class="text-gray-600 hover:text-gray-900">Rental deals</a>
        <a href="#" class="text-gray-600 hover:text-gray-900">How it work</a>
        <a href="#" class="text-gray-600 hover:text-gray-900">Why choose us</a>
      </nav>
      <div class="flex items-center space-x-4">
        <NuxtLink to="/auth/signin" class="text-gray-600 hover:text-gray-900"
          ><Button variant="outline" class="text-black bg-transparent"
            >Sign in</Button
          ></NuxtLink
        >
        <NuxtLink to="/auth/signup" class="text-white"
          ><Button class="bg-blue-600 hover:bg-blue-700 text-white"
            >Sign up</Button
          ></NuxtLink
        >
      </div>
    </header>
    <main class="flex-1 flex">
      <slot />
    </main>
    <footer class="bg-gray-900 text-white py-16">
      <div class="container mx-auto px-4">
        <div class="grid md:grid-cols-4 gap-8">
          <div>
            <div class="flex items-center mb-4">
              <img
                src="/Logo.webp"
                alt="image"
                width="100"
                height="100"
                class="ci"
              />
            </div>
            <p class="text-gray-400">The best car rental service in town</p>
          </div>
          <div>
            <h4 class="font-semibold mb-4">Our Product</h4>
            <ul class="space-y-2 text-gray-400">
              <li><a href="#" class="hover:text-white">Cars</a></li>
              <li><a href="#" class="hover:text-white">Car with driver</a></li>
              <li><a href="#" class="hover:text-white">Delivery</a></li>
            </ul>
          </div>
          <div>
            <h4 class="font-semibold mb-4">Resources</h4>
            <ul class="space-y-2 text-gray-400">
              <li><a href="#" class="hover:text-white">Help center</a></li>
              <li><a href="#" class="hover:text-white">Guides</a></li>
              <li><a href="#" class="hover:text-white">Partner</a></li>
            </ul>
          </div>
          <div>
            <h4 class="font-semibold mb-4">Follow Us</h4>
            <div class="flex space-x-4">
              <a href="#" class="hover:text-blue-400">
                <Facebook class="w-6 h-6" />
              </a>
              <a href="#" class="hover:text-blue-400">
                <Twitter class="w-6 h-6" />
              </a>
              <a href="#" class="hover:text-blue-400">
                <Instagram class="w-6 h-6" />
              </a>
            </div>
          </div>
        </div>
      </div>
    </footer>
  </div>
</template>
