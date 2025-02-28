<template>
  <div class="w-full sm:w-10/12 md:w-8/12 lg:w-6/12 px-4 sm:px-10 md:px-20 py-6 sm:py-10">
    <form @submit.prevent="saveForm">
      <!-- Future Date Input -->
      <div class="mb-4">
        <label for="futureDate" class="block text-sm sm:text-base font-medium text-gray-700">
          Date
        </label>
        <input
          type="date"
          id="futureDate"
          v-model="formData.futureDate"
          :min="minDate"
          class="mt-2 block w-full px-3 py-2 border border-[#707070] rounded-md text-sm sm:text-base"
          required
        />
      </div>

      <!-- Title Input -->
      <div class="mb-4">
        <label for="title" class="block text-sm sm:text-base font-medium text-gray-700">
          Title
        </label>
        <input
          type="text"
          id="title"
          v-model="formData.title"
          class="mt-2 block w-full px-3 py-2 border border-[#707070] rounded-md text-sm sm:text-base"
          required
        />
      </div>

      <!-- Details Input -->
      <div class="mb-6">
        <label for="details" class="block text-sm sm:text-base font-medium text-gray-700">
          Details
        </label>
        <textarea
          id="details"
          v-model="formData.details"
          rows="4"
          class="mt-2 block w-full h-70 px-3 py-2 border border-[#707070] rounded-md text-sm sm:text-base"
          required
        ></textarea>
      </div>

      <!-- Submit Button -->
      <div class="flex justify-center mt-8 mb-10">
        <button
          type="submit"
          class="bg-[#5271FF] w-full sm:w-6/12 md:w-4/12 py-2 sm:py-3 rounded-md text-sm sm:text-base text-white  hover:bg-amber-300 cursor-pointer"
        >
          Save
        </button>
      </div>
    </form>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue';
import { useRouter } from 'vue-router';

const router = useRouter();

// Form data
const formData = ref({
  futureDate: '', // Updated to match backend field name
  title: '',
  details: '',
});

// Minimum date for the date input (today's date)
const minDate = new Date().toISOString().split('T')[0];

// Save form data to the API
const saveForm = async () => {
  try {
    // Get the token from localStorage
    const token = localStorage.getItem('token');
    if (!token) {
      router.push('/');
      return;
    }

    // Log the request body for debugging
    console.log('Request Body:', JSON.stringify(formData.value));

    // Send a POST request to the API
    const response = await $fetch('https://eventful-moments-api.onrender.com/api/v1/moment', {
      method: 'POST',
      headers: {
        'Authorization': `Bearer ${token}`,
        'Content-Type': 'application/json',
      },
      body: JSON.stringify(formData.value),
    });

    // Handle successful response
    if (response) {
      alert('Moment created successfully!');
      router.push('/items/allmoment'); // Redirect to the allmoments page
    }
  } catch (error) {
    console.error('Error creating moment:', error);
    if (error.data) {
      console.error('API Error Response:', error.data); // Log the API error response
    }
    alert('An error occurred while creating the moment. Please try again.');
  }
};
</script>

<style scoped>
/* Add your custom styles here */
</style>