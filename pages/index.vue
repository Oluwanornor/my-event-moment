<template>
    <div class="flex flex-col justify-center items-center min-h-[80vh] py-20">
      <div class="w-full sm:w-10/12 md:w-8/12 lg:w-6/12 xl:w-4/12 flex flex-col gap-5 px-4 sm:px-0">
        <h1 class="text-2xl sm:text-3xl md:text-4xl font-bold text-center sm:text-left">
          Welcome back,
        </h1>
        <p class="text-base sm:text-lg md:text-xl text-center sm:text-left">
          Hi, my name is Eventful Moments, I am a bucket… no, not the bucket of
          water but I store awesome moments you will like to have in coming years.
        </p>
        <form @submit.prevent="loginUser">
          <div class="mb-4 flex flex-col gap-3">
            <label for="email" class="block text-sm sm:text-base text-gray-700">
              Email
            </label>
            <input
              type="text"
              v-model="email"
              id="email"
              class="block w-full h-12 sm:h-13 px-3 py-2 border border-[#707070] rounded-md text-sm sm:text-base"
            />
            <span class="text-red-500 text-sm">{{ errors.email }}</span>
          </div>
          <div class="mb-4 flex flex-col gap-3">
            <label for="password" class="block text-sm sm:text-base text-gray-700">
              Password
            </label>
            <input
              type="password"
              v-model="password"
              id="password"
              class="block w-full h-12 sm:h-13 px-3 py-2 border border-[#707070] rounded-md text-sm sm:text-base"
            />
            <span class="text-red-500 text-sm">{{ errors.password }}</span>
          </div>
          <div class="flex justify-center mt-8 mb-10">
            <button
              type="submit"
              class="w-full text-center sm:w-8/12 md:w-6/12 lg:w-4/12 py-2 sm:py-3 px-4 sm:px-7 rounded-xl text-sm sm:text-base text-white bg-[#5271FF]  hover:bg-amber-300 cursor-pointer"
            >
              Login
            </button>
          </div>
        </form>
      </div>
    </div>
  </template>
  
  <script setup>
  import { ref } from 'vue';
  import { useRouter } from 'vue-router';
  
  const email = ref('');
  const password = ref('');
  const errors = ref({
    email: '',
    password: '',
  });
  const router = useRouter();
  
  const validateForm = () => {
    errors.value.email = '';
    errors.value.password = '';
  
    if (!email.value) {
      errors.value.email = 'Email is required';
    } else if (!/\S+@\S+\.\S+/.test(email.value)) {
      errors.value.email = 'Email is invalid';
    }
  
    if (!password.value) {
      errors.value.password = 'Password is required';
    } else if (password.value.length < 6) {
      errors.value.password = 'Password must be at least 6 characters';
    }
  
    return !errors.value.email && !errors.value.password;
  };
  
  const loginUser = async () => {
    if (!validateForm()) return;
  
    try {
      const response = await $fetch('https://eventful-moments-api.onrender.com/api/v1/users/login', {
        method: 'POST',
        body: JSON.stringify({
          email: email.value,
          password: password.value,
        }),
        headers: {
          'Content-Type': 'application/json',
        },
      });
  
      if (response) {
        // Assuming the response contains a token or user data
        localStorage.setItem('token', response.token); // Save token to localStorage
        router.push('/items/allmoment'); // Redirect to the desired route
      }
    } catch (error) {
      console.error('Login failed:', error);
      errors.value.email = 'Login failed. Please check your credentials.';
    }
  };
  </script>