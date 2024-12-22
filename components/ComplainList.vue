<template>
    <div class="bg-white/80 backdrop-blur-sm rounded-xl shadow-lg border border-blue-100 p-8 space-y-6">
        <!-- Header -->
        <div class="flex items-center justify-between">
            <div class="flex items-center gap-3">
                <ListTodo class="w-6 h-6 text-blue-600" />
                <h2 class="text-2xl font-semibold text-gray-900">Complaints List</h2>
            </div>
            <div class="flex flex-col gap-2">
                <div class="flex items-center gap-2 text-sm text-gray-500">
                    <Archive class="w-4 h-4" />
                    <span>{{ complaints.length }} complaints</span>

                </div>
                <div class="flex items-center gap-2 text-sm text-gray-500">
                    <Eye class="w-4 h-4" />
                    <span>{{ limit }} showing</span>

                </div>
            </div>
        </div>

        <!-- Loading State -->
        <div v-if="isLoading || isSaving" class="py-12 text-center">

            <p class="text-gray-500">Loading complaints...</p>
            <Skeliton></Skeliton>
        </div>
        <div v-else-if="isError" class="py-12 text-center">
            <XCircle class="w-12 h-12 mx-auto text-red-500 mb-3" />
            <p class="text-lg text-gray-500">Something went wrong</p>
            <button @click="refreshAll()" class="text-sm text-gray-400">
                Try again
            </button>
        </div>

        <!-- Empty State -->
        <div v-else-if="complaints.length === 0" class="py-12 text-center">
            <InboxIcon class="w-12 h-12 mx-auto text-gray-400 mb-3" />
            <p class="text-lg text-gray-500">No complaints available.</p>
            <p class="text-sm text-gray-400">Submit your first complaint above.</p>
        </div>

        <!-- Complaints List -->
        <div v-else class="space-y-4">
            <div v-for="complaint in complaints.slice(0, limit)" :key="complaint.id"
                class="group p-6 rounded-lg border-2 shadow-sm border-gray-100 hover:border-blue-200 hover:bg-blue-50/50 transition-all">
                <div class="flex items-start justify-between gap-4">
                    <div class="space-y-2">
                        <h3 class="text-lg font-medium text-gray-900 group-hover:text-blue-600 transition-colors">
                            {{ complaint.Title }}
                        </h3>
                        <p class="text-gray-600">{{ complaint.Body }}</p>
                    </div>
                </div>
                <div class="mt-4 flex items-center gap-2 text-sm text-gray-400">
                    <Calendar class="w-4 h-4 text-blue-600" />
                    {{ dateConverter(complaint.CreatedAt) }}
                </div>
            </div>
            <button @click="viewMore" v-if="complaints.length > 5 && limit < complaints.length"
                class="ml-auto px-6 py-3 mx-auto rounded-full bg-blue-600 text-white hover:bg-blue-700 focus:ring-2 focus:ring-blue-500/20 disabled:opacity-50 disabled:cursor-not-allowed transition-all flex items-center gap-2">
                <ArrowDownCircleIcon class="w-4 h-4"></ArrowDownCircleIcon>
                <span>View More</span>
            </button>
            <button @click="viewLess" v-else-if="complaints.length == limit"
                class="ml-auto px-6 py-3 mx-auto rounded-full bg-blue-600 text-white hover:bg-blue-700 focus:ring-2 focus:ring-blue-500/20 disabled:opacity-50 disabled:cursor-not-allowed transition-all flex items-center gap-2">
                <ArrowUpCircle class="w-4 h-4"></ArrowUpCircle>
                <span>View Less</span>
            </button>

        </div>
        <div>

        </div>
    </div>
</template>

<script setup>
import { ListTodo, Archive, Calendar, InboxIcon, ArrowDownCircleIcon, ArrowUpCircle, Eye, Cross, RefreshCcw, XCircle } from 'lucide-vue-next'

// Define props
const props = defineProps({
    complaints: {
        type: Array,
        default: () => []
    },
    isLoading: {
        type: Boolean,
        default: false
    },
    isError: {
        type: String
    },
    isSaving: {
        type: Boolean,
        default: false
    }
})
const limit = ref(5)
const dateConverter = (dateString) => {
    const options = {
        year: "numeric",
        month: "long", // Full month name (e.g., "December")
        day: "numeric",
        hour: "numeric",
        minute: "numeric",
        second: "numeric",

    };
    const date = new Date(dateString)
    const utcDate = new Intl.DateTimeFormat("en-US", options).format(date)
    return utcDate
}

const viewMore = () => {
    limit.value += 5
}
const viewLess = () => {
    limit.value -= 5
}
const refreshing = ref(false)
const refreshAll = async () => {
    refreshing.value = true
    try {
        await refreshNuxtData()
    } finally {
        refreshing.value = false
    }
}
</script>