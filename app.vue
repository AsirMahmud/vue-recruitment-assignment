<template>
  <div class="min-h-screen bg-gradient-to-br from-blue-50 to-indigo-50 p-6">
    <div class="max-w-4xl mx-auto space-y-8">
      <!-- Logo -->
      <div class="flex items-center justify-center gap-2 mb-8">
        <MessageSquare class="w-8 h-8 text-blue-600" />
        <h1 class="text-2xl font-bold bg-gradient-to-r from-blue-600 to-indigo-600 bg-clip-text text-transparent">
          Feedbacker
        </h1>
      </div>

      <!-- Complaint Form -->
      <div
        class="bg-white/80 backdrop-blur-sm rounded-xl shadow-lg border border-blue-100 p-8 space-y-6 transition-all hover:shadow-xl">
        <div class="flex items-center gap-3">
          <PencilLine class="w-6 h-6 text-blue-600" />
          <h2 class="text-2xl font-semibold text-gray-900">Submit a Complaint</h2>
        </div>

        <div class="space-y-4">
          <div class="space-y-2">
            <label class="text-sm font-medium text-gray-700">Title</label>
            <input v-model="title" type="text" placeholder="Enter complaint title"
              class="w-full px-4 py-3 rounded-lg border border-gray-200 focus:border-blue-500 focus:ring-2 focus:ring-blue-500/20 outline-none transition-all placeholder:text-gray-400" />
          </div>

          <div class="space-y-2">
            <label class="text-sm font-medium text-gray-700">Description</label>
            <textarea v-model="body" rows="4" placeholder="Describe your complaint in detail..."
              class="w-full px-4 py-3 rounded-lg border border-gray-200 focus:border-blue-500 focus:ring-2 focus:ring-blue-500/20 outline-none transition-all placeholder:text-gray-400 resize-none"></textarea>
          </div>

          <div class="flex items-center justify-between pt-2">

            <button @click="saveComplain" :disabled="isSaving || !title || !body"
              class="ml-auto px-6 py-3 rounded-lg bg-blue-600 text-white hover:bg-blue-700 focus:ring-2 focus:ring-blue-500/20 disabled:opacity-50 disabled:cursor-not-allowed transition-all flex items-center gap-2">
              <Send v-if="!isSaving" class="w-4 h-4" />
              <Loader2 v-else class="w-4 h-4 animate-spin" />
              <span>{{ isSaving ? 'Submitting...' : 'Submit Complaint' }}</span>
            </button>
          </div>
        </div>
      </div>

      <ComplainList :complaints="complains" :is-loading="isLoading" :is-error="errorMessage"></ComplainList>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue'
import {
  MessageSquare,
  PencilLine,
  Send,
  Loader2,

} from 'lucide-vue-next'
const config = useRuntimeConfig()
const baseUrl = config.public.base_url;
const listPath = "TestApi/GetComplains";
const savePath = "TestApi/SaveComplain";

const complains = ref([]);
const title = ref("");
const body = ref("");
const isSaving = ref(false);
const errorMessage = ref("");
const isLoading = ref(false);

// Fetch complaints from the API
const fetchComplains = async () => {
  try {
    isLoading.value = true;
    const response = await fetch(`${baseUrl}${listPath}`);
    const data = await response.json();
    complains.value = data;
    isLoading.value = false;
  } catch (e) {
    errorMessage.value = e
    console.log(e)
  } finally {
    isLoading.value = false
  }
};

// Save a new complaint
const saveComplain = async () => {
  try {
    isSaving.value = true;
    const response = await fetch(`${baseUrl}${savePath}`, {
      method: "POST",
      headers: {
        "Content-Type": "application/json",
      },
      body: JSON.stringify({
        Title: title.value,
        Body: body.value,
      }),
    });
    const data = await response.json();
    console.log(data);
    if (!data.Success) throw new Error("Failed to save complaint.");
    // update list of conplaints on success
    fetchComplains()
    title.value = ''
    body.value = ''
  } catch (e) {
    // handle error
    errorMessage.value = e
    console.error(e)
  } finally {
    isSaving.value = false;
  }
};

onMounted(() => {
  fetchComplains()
});
</script>
