<script setup>
import { reactive, onMounted } from "vue";
import { useRoute, RouterLink } from "vue-router";

import BackButton from "@/components/BackButton.vue";

import PulseLoader from "vue-spinner/src/PulseLoader.vue";

const route = useRoute();
// The route var is used to check the current route to know which job listing is being viewed
const jobId = route.params.id;
const state = reactive({
  job: {},
  isLoading: true,
});

// GET request to backend to retrieve jobs data
async function getJobsDataById() {
  const url = `/api/jobs/${jobId}`;
  try {
    const response = await fetch(url);
    if (!response.ok) {
      throw new Error(`Response status: ${response.status}`);
    }

    const json = await response.json();
    state.job = json;
  } catch (error) {
    console.error(`Error fetching job with ID ${jobId}:`, error.message);
  } finally {
    // so spinner animation knows when to stop
    state.isLoading = false;
  }
}

onMounted(async () => {
  getJobsDataById();
});
</script>

<template>
  <BackButton />

  <!-- Show loading spinner while state.isLoading is true/data hasn't been fetched yet -->
  <div v-if="state.isLoading" class="text-center text-gray-500 py-6">
    <PulseLoader />
  </div>

  <!-- Only show section once state.isLoading is set to true (AKA if the job data with the specific ID has been successfully fetched) -->
  <section v-else class="bg-green-50">
    <div class="container m-auto py-10 px-6">
      <div class="grid grid-cols-1 md:grid-cols-70/30 w-full gap-6">
        <main>
          <div
            class="bg-white p-6 rounded-lg shadow-md text-center md:text-left"
          >
            <div class="text-gray-500 mb-4">{{ state.job.type }}</div>
            <h1 class="text-3xl font-bold mb-4">{{ state.job.title }}</h1>
            <div
              class="text-gray-500 mb-4 flex align-middle justify-center md:justify-start"
            >
              <span class="mdi mdi-map-marker text-orange-500 mr-2"></span>
              <p class="text-orange-700">{{ state.job.location }}</p>
            </div>
          </div>

          <div class="bg-white p-6 rounded-lg shadow-md mt-6">
            <h3 class="text-green-800 text-lg font-bold mb-6">
              Job Description
            </h3>

            <div class="mb-4 whitespace-pre-line">
              {{ state.job.description }}
            </div>

            <h3 class="text-green-800 text-lg font-bold mb-2">Salary</h3>

            <p class="mb-4">{{ state.job.salary }} / Year</p>
          </div>
        </main>

        <!-- Sidebar -->
        <aside>
          <!-- Company Info -->
          <div class="bg-white p-6 rounded-lg shadow-md">
            <h3 class="text-xl font-bold mb-6">Company Info</h3>

            <h2 class="text-2xl">{{ state.job.company.name }}</h2>

            <p class="my-2">
              {{ state.job.company.description }}
            </p>

            <hr class="my-4" />

            <h3 class="text-xl">Contact Email:</h3>

            <p class="my-2 bg-green-100 p-2 font-bold">
              {{ state.job.company.contactEmail }}
            </p>

            <h3 class="text-xl">Contact Phone:</h3>

            <p class="my-2 bg-green-100 p-2 font-bold">
              {{ state.job.company.contactPhone }}
            </p>
          </div>

          <!-- Manage -->
          <div class="bg-white p-6 rounded-lg shadow-md mt-6">
            <h3 class="text-xl font-bold mb-6">Manage Job</h3>
            <RouterLink
              :to="`/jobs/edit/${state.job.id}`"
              class="bg-green-500 hover:bg-green-600 text-white text-center font-bold py-2 px-4 rounded-full w-full focus:outline-none focus:shadow-outline mt-4 block"
              >Edit Job</RouterLink
            >
            <button
              class="bg-red-500 hover:bg-red-600 text-white font-bold py-2 px-4 rounded-full w-full focus:outline-none focus:shadow-outline mt-4 block"
            >
              Delete Job
            </button>
          </div>
        </aside>
      </div>
    </div>
  </section>
</template>
