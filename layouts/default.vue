<template>
    <div>
      <header>
        <nav
          class="flex flex-row justify-between bg-[#008289] text-white px-4 sm:px-8 md:px-16 h-16 md:h-20 items-center"
        >
          <div>
            <NuxtLink
              to="/"
              class="text-lg md:text-xl lg:text-2xl italic font-semibold"
              >Eventful Moments</NuxtLink
            >
          </div>
          <ul class="flex flex-row gap-4">
            <li v-if="showAuthLinks"><NuxtLink to="/">Login</NuxtLink></li>
            <li v-if="showAuthLinks">
              <NuxtLink to="/signup">Register</NuxtLink>
            </li>
            <li v-if="!showAuthLinks">
              <NuxtLink to="/items/allmoment">My Bucket</NuxtLink>
            </li>
            <li v-if="!showAuthLinks">
              <NuxtLink to="/">Logout</NuxtLink>
            </li>
          </ul>
        </nav>
      </header>
      <div class="mx-auto">
        <slot />
        <Footer />
      </div>
    </div>
  </template>
  
  <script setup>
  const route = useRoute()
  const authPages = ['/', '/signup'] // Pages where Login/Register should show
  
  const showAuthLinks = computed(() => authPages.includes(route.path))
  </script>
  
  <style  scoped></style>
  