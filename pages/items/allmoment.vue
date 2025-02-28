<template>
  <div>
    <!-- Header Section -->
    <div class="flex flex-col sm:flex-row justify-between px-4 sm:px-10 md:px-20 py-6 sm:py-10">
      <div>
        <h1 class="text-2xl sm:text-3xl md:text-4xl font-semibold">Welcome, {{ fullName }}</h1>
        <p class="text-sm sm:text-base">Here are items in your eventful moment bucket.</p>
      </div>
      <div class="mt-4 sm:mt-0">
        <NuxtLink
          to="/addnewmoment"
          @click.native="checkAuthBeforeNavigation"
          class="bg-[#5271FF] hover:bg-amber-400 w-full sm:w-auto px-6 py-2 sm:px-8 sm:py-3 rounded-md text-sm sm:text-base text-white"
        >
          Add item
        </NuxtLink>
      </div>
    </div>

    <!-- Grid Section -->
    <div v-if="loading" class="flex justify-center items-center h-40">
      <p class="text-sm sm:text-base">Loading items...</p>
    </div>
    <div v-else-if="error" class="flex justify-center items-center h-40">
      <p class="text-sm sm:text-base text-red-500">{{ error }}</p>
    </div>
    <div v-else class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-2 gap-6 px-4 sm:px-10 md:px-20">
      <div
        v-for="(item, index) in visibleItems"
        :key="item._id"
        class="p-4 sm:p-6 shadow-sm hover:bg-[#521] cursor-pointer"
      >
        <h2 class="text-sm sm:text-base font-bold">{{ item.title }}</h2>
        <p class="text-xs sm:text-sm text-justify mt-2 leading-7">{{ truncateDetails(item.details) }}</p>
        <div class="flex flex-row justify-between mt-4 hover:bg-amber-400">
          <NuxtLink :to="`/items/${item._id}`" class="text-blue-500 text-xs sm:text-sm">View Details</NuxtLink>
          <div class="flex flex-row gap-2 sm:gap-3">
            <p class="text-xs sm:text-sm text-gray-300">{{ formatDate(item.createdAt) }}</p>
            <p class="text-xs sm:text-sm ">{{ formatDate(item.futureDate) }}</p>
          </div>
        </div>
      </div>
    </div>

    <!-- Load More Button -->
    <div v-if="showLoadMore" class="flex justify-center mt-10 mb-20">
      <button
        @click="loadMore"
        class="bg-[#5271FF] text-center w-40 sm:w-48 py-2 sm:py-3 rounded-xl text-sm sm:text-base text-white"
      >
        Load More
      </button>
    </div>
  </div>
</template>

<script setup>
import { ref, computed, onMounted, watch } from 'vue';
import { useRouter } from 'vue-router';

const router = useRouter();
const fullName = ref(''); // Ensure reactivity
const items = ref([]);
const loading = ref(true);
const error = ref('');
const totalLoaded = ref(4); // Start with 4 items

// Computed property for visible items
const visibleItems = computed(() => items.value.slice(0, totalLoaded.value));

// Computed property for "Load More" button visibility
const showLoadMore = computed(() => totalLoaded.value < items.value.length);

// Fetch full name from localStorage when component is mounted
const fetchUserData = () => {
  const storedFullName = localStorage.getItem('fullName');
  if (storedFullName) {
    fullName.value = storedFullName;
  }
};

// Check authentication before navigation
const checkAuthBeforeNavigation = () => {
  const token = localStorage.getItem('token');
  if (!token) {
    alert('You must be logged in to add a new moment.');
    router.push('/');
  } else {
    router.push('/addnewmoment');
  }
};

// Fetch items from the API
const fetchItems = async () => {
  try {
    loading.value = true;
    error.value = '';

    const token = localStorage.getItem('token');
    if (!token) {
      router.push('/');
      return;
    }

    const response = await $fetch('https://eventful-moments-api.onrender.com/api/v1/moment', {
      method: 'GET',
      headers: {
        'Authorization': `Bearer ${token}`,
        'Content-Type': 'application/json',
      },
    });

    console.log('API Response:', response); // Debugging

    if (response && response.data && Array.isArray(response.data[0].data)) {
      items.value = response.data[0].data;
    } else {
      error.value = 'No items found.';
    }
  } catch (err) {
    console.error('Error fetching items:', err);
    error.value = 'An error occurred while fetching items. Please try again.';
  } finally {
    loading.value = false;
  }
};

// Load more items (2 at a time)
const loadMore = () => {
  totalLoaded.value += 2;
};

// Truncate details text
const truncateDetails = (details) => {
  if (!details) return '';
  return details.length > 500 ? details.slice(0, 500) + '...' : details;
};

// Format date safely
const formatDate = (dateString) => {
  if (!dateString) return 'N/A';
  const date = new Date(dateString);
  return isNaN(date) ? 'Invalid Date' : date.toLocaleDateString('en-GB', { day: 'numeric', month: 'short', year: 'numeric' });
};

// Watch for new items being added
watch(items, (newItems) => {
  if (newItems.length > 0) {
    totalLoaded.value = 4; // Reset to 4 items when new data arrives
  }
});

// Fetch data on mount
onMounted(() => {
  fetchUserData(); // Load user data
  fetchItems(); // Fetch items
});
</script>




<style scoped>
/* Add custom styles here */
</style>
