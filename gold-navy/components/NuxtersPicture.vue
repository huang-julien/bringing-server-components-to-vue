<template>
    <img :src="src" alt="Nuxters" />
</template>

<script setup lang="ts">
defineProps<{
    src: string
}>()
</script>
