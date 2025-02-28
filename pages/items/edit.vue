<template>
  <div class="w-full sm:w-10/12 md:w-8/12 lg:w-6/12 px-4 sm:px-10 md:px-20 py-6 sm:py-10">
    <form @submit.prevent="updateItem">
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
          Update
        </button>
      </div>
    </form>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue';
import { useRoute, useRouter } from 'vue-router';

const route = useRoute();
const router = useRouter();
const itemId = ref(route.query.id); // Get ID from query params
const formData = ref({
  title: '',
  futureDate: '',
  details: '',
});
const minDate = new Date().toISOString().split('T')[0]; // Set minimum date to today

// Fetch Item Data
const fetchItem = async () => {
  try {
    const token = localStorage.getItem('token');
    if (!token) {
      router.push('/');
      return;
    }

    const response = await fetch(
      `https://eventful-moments-api.onrender.com/api/v1/moment/${itemId.value}`,
      {
        method: 'GET',
        headers: { Authorization: `Bearer ${token}` },
      }
    );

    if (!response.ok) {
      throw new Error('Failed to fetch item');
    }

    const data = await response.json();
    if (data.data) {
      formData.value = {
        title: data.data.title || '',
        futureDate: data.data.futureDate ? data.data.futureDate.split('T')[0] : '',
        details: data.data.details || '',
      };
    }
  } catch (err) {
    console.error('Error fetching item:', err);
    alert('Failed to fetch item. Please try again.');
  }
};

// Fetch data when component is mounted
onMounted(() => {
  if (itemId.value) {
    fetchItem();
  } else {
    console.error('No item ID provided');
    router.push('/'); // Redirect if no ID is provided
  }
});

// Update Item
const updateItem = async () => {
  try {
    const token = localStorage.getItem('token');
    if (!token) {
      router.push('/');
      return;
    }

    const updatedData = {
      title: formData.value.title,
      futureDate: formData.value.futureDate,
      details: formData.value.details,
    };

    const response = await fetch(
      `https://eventful-moments-api.onrender.com/api/v1/moment/${itemId.value}`,
      {
        method: 'PATCH',
        headers: {
          Authorization: `Bearer ${token}`,
          'Content-Type': 'application/json',
        },
        body: JSON.stringify(updatedData),
      }
    );

    if (!response.ok) {
      throw new Error('Failed to update item');
    }

    alert('Item updated successfully!');
    router.push(`/items/${itemId.value}`);
  } catch (err) {
    console.error('Error updating item:', err);
    alert('Failed to update item. Please try again.');
  }
};
</script>

<style scoped>
/* Add custom styles here */
</style>