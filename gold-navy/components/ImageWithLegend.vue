<template>
    <div class="bg-pink-200 p-3 rounded-lg">
        <img :src="src" :class="imageClass" />

        <div class="text-center text-gray-500 text-sm mt-2">
            <slot name="legend">
                {{ legend }}
            </slot>
        </div>
    </div>
</template>


<script setup lang="ts">

defineProps<{
    src: string
    legend: string
    imageClass?: string
}>()
</script>