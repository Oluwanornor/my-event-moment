<template>
  <div
    class="w-full sm:w-10/12 md:w-8/12 lg:w-12/12 px-4 sm:px-10 md:px-20 py-6 sm:py-10"
  >
    <div v-if="loading" class="flex justify-center items-center h-40">
      <p class="text-sm sm:text-base">Loading item...</p>
    </div>
    <div v-else-if="error" class="flex justify-center items-center h-40">
      <p class="text-sm sm:text-base text-red-500">{{ error }}</p>
    </div>
    <div v-else>
      <div class="mb-6">
        <h1 class="text-2xl sm:text-3xl font-semibold">{{ item.title }}</h1>
        <p class="text-sm sm:text-base text-blue-500 mt-2">
          {{ formatDate(item.futureDate) }}
        </p>
      </div>

      <div class="mb-6">
        <p class="text-sm sm:text-base">{{ item.details }}</p>
      </div>

      <div class="flex gap-2 mt-8">
        <button
          @click="editItem"
          class="bg-[#5271FF] hover:bg-amber-300 cursor-pointer px-15 py-3 rounded-md text-sm sm:text-base text-white"
        >
          Edit
        </button>
        <button
          @click="deleteItem"
          class="bg-red-500 hover:bg-amber-300 cursor-pointer px-15 py-3 rounded-md text-sm sm:text-base text-white"
        >
          Delete
        </button>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted } from "vue";
import { useRouter, useRoute } from "vue-router";

const router = useRouter();
const route = useRoute();

// Item data
const item = ref({});
const loading = ref(true); // Loading state
const error = ref(""); // Error message

// Get the item ID from the route
const itemId = route.params.id;

// Fetch the single item from the API
const fetchItem = async () => {
  try {
    if (!itemId) {
      throw new Error("Item ID is undefined");
    }

    // Get the token from localStorage
    const token = localStorage.getItem("token");
    if (!token) {
      router.push("/");
      return;
    }

    // Fetch the item from the API
    const response = await $fetch(
      `https://eventful-moments-api.onrender.com/api/v1/moment/${itemId}`,
      {
        method: "GET",
        headers: {
          Authorization: `Bearer ${token}`,
          "Content-Type": "application/json",
        },
      }
    );

    // Set the item data
    if (response.data) {
      item.value = response.data;
    } else {
      throw new Error("Item not found");
    }
  } catch (error) {
    console.error("Error fetching item:", error);
    error.value =
      "An error occurred while fetching the item. Please try again.";
  } finally {
    loading.value = false;
  }
};

// Edit the item
const editItem = () => {
  router.push(`/items/edit?id=${itemId}`);
};

// Delete the item
const deleteItem = async () => {
  try {
    const token = localStorage.getItem("token");
    if (!token) {
      router.push("/");
      return;
    }

    const response = await $fetch(
      `https://eventful-moments-api.onrender.com/api/v1/moment/${itemId}`,
      {
        method: "DELETE",
        headers: {
          Authorization: `Bearer ${token}`,
          "Content-Type": "application/json",
        },
      }
    );

    if (response) {
      alert("Item deleted successfully!");
      router.push("/items/allmoment");
    }
  } catch (error) {
    console.error("Error deleting item:", error);
    alert("An error occurred while deleting the item. Please try again.");
  }
};

// Format date to a readable format
const formatDate = (dateString) => {
  if (!dateString) return ""; // Handle undefined or null date
  const date = new Date(dateString);
  return date.toLocaleDateString("en-GB", {
    day: "numeric",
    month: "short",
    year: "numeric",
  });
};

// Fetch the item when the component mounts
onMounted(() => {
  fetchItem();
});
</script>

<style scoped>
/* Add your custom styles here */
</style>
