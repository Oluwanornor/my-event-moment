<template>
  <div class="flex flex-col justify-center items-center min-h-[80vh] py-10">
    <div class="w-full sm:w-10/12 md:w-8/12 lg:w-6/12 xl:w-4/12 flex flex-col gap-5 px-4 sm:px-0">
      <h1 class="text-2xl sm:text-3xl md:text-4xl font-semibold text-center sm:text-left">
        Create an account,
      </h1>

      <form @submit.prevent="registerUser">
        <!-- Full Name Input -->
        <div class="mb-4 flex flex-col gap-3">
          <label for="fullname" class="block text-sm sm:text-base text-gray-700">
            Full Name
          </label>
          <input
            type="text"
            v-model="fullname"
            id="fullname"
            class="block w-full h-12 sm:h-13 px-3 py-2 border border-[#707070] rounded-md text-sm sm:text-base"
          />
          <span class="text-red-500 text-sm">{{ errors.fullname }}</span>
        </div>

        <!-- Email Input -->
        <div class="mb-4 flex flex-col gap-3">
          <label for="email" class="block text-sm sm:text-base text-gray-700">
            Email
          </label>
          <input
            type="email"
            v-model="email"
            id="email"
            class="block w-full h-12 sm:h-13 px-3 py-2 border border-[#707070] rounded-md text-sm sm:text-base"
          />
          <span class="text-red-500 text-sm">{{ errors.email }}</span>
        </div>

        <!-- Password Input -->
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

        <!-- Submit Button -->
        <div class="flex justify-center mt-8 mb-10">
          <button
            type="submit"
            class="w-full sm:w-8/12 md:w-6/12 lg:w-4/12 py-2 sm:py-3 px-4 sm:px-7 rounded-xl text-sm sm:text-base text-white bg-[#5271FF] hover:bg-amber-300 cursor-pointer"
          >
            Create
          </button>
        </div>
      </form>
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue';
import { useRouter } from 'vue-router';

const fullname = ref('');
const email = ref('');
const password = ref('');
const errors = ref({
  fullname: '',
  email: '',
  password: '',
});
const router = useRouter();

const validateForm = () => {
  errors.value.fullname = '';
  errors.value.email = '';
  errors.value.password = '';

  if (!fullname.value) {
    errors.value.fullname = 'Full Name is required';
  } else if (fullname.value.length < 10) {
    errors.value.fullname = 'Full Name must be at least 10 characters';
  }

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

  return !errors.value.fullname && !errors.value.email && !errors.value.password;
};

const registerUser = async () => {
  if (!validateForm()) return;

  try {
    const response = await $fetch('https://eventful-moments-api.onrender.com/api/v1/users/signup', {
      method: 'POST',
      body: JSON.stringify({
        fullname: fullname.value,
        email: email.value,
        password: password.value,
      }),
      headers: {
        'Content-Type': 'application/json',
      },
    });

    if (response) {
      localStorage.setItem('token', response.token); // Save token to localStorage
      localStorage.setItem('fullName', fullname.value); // Save full name to localStorage
      alert('Account Created Successfully');
      router.push('/'); // Redirect to the login page or another route
    }
  } catch (error) {
    console.error('Registration failed:', error);
    errors.value.email = 'Registration failed. Please try again.';
  }
};
</script>

<style scoped></style>
