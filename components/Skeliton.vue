<template>
    <div class="space-y-4">
        <!-- Repeat skeleton cards based on count prop -->
        <div v-for="n in count" :key="n" class="group p-6 rounded-lg border border-gray-100 bg-white/80 animate-pulse">
            <div class="flex items-start justify-between gap-4">
                <div class="space-y-2 w-full">
                    <!-- Title skeleton -->
                    <div class="h-6 bg-gray-200 rounded-md w-3/4"></div>

                    <!-- Body skeleton - Multiple lines -->
                    <div class="space-y-2">
                        <div class="h-4 bg-gray-200 rounded-md w-full"></div>
                        <div class="h-4 bg-gray-200 rounded-md w-5/6"></div>
                        <div class="h-4 bg-gray-200 rounded-md w-4/6"></div>
                    </div>
                </div>
            </div>

            <!-- Date skeleton -->
            <div class="mt-4 flex items-center gap-2">
                <div class="h-4 w-4 bg-gray-200 rounded-full"></div>
                <div class="h-4 bg-gray-200 rounded-md w-24"></div>
            </div>
        </div>
    </div>
</template>

<script setup>
defineProps({
    count: {
        type: Number,
        default: 3
    }
})
</script>