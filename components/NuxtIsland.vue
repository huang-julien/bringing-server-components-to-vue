<template>
    <div class="html bg-green-500">
        <div class="text-white">
            Header
        </div>

        <span class="grid-cols-2 gap-4 grid">
            <div class="bg-black-500 text-white">
                Sidebar
            </div>

            <div class="bg-black-500 text-white border-none !p-0">
                <div>
                    Main content
                </div>
                <div>
                    Carousel

                    <span class="flex gap-px text-xs justify-between">

                        <div :class="{ 'bg-black': clicks > 0 }" class="!p-0 items-center">
                            Item 1
                        </div>
                        <div :class="{ 'bg-black': clicks > 0 }" class="!p-0 items-center">
                            Item 2
                        </div>
                        <div :class="{ 'bg-black': clicks > 0 }" class="!p-0 items-center">
                            Item 3
                        </div>

                    </span>
                </div>
            </div>
        </span>

        <div class="bg-black-500 text-white"  :class="{ 'bg-black': clicks > 0 }" >
            Footer
        </div>
    </div>
</template>

<script setup lang="ts">
import { useSlideContext } from '@slidev/client'

const clicks = useSlideContext().$clicks
</script>

<style lang="scss" scoped>
div {
    @apply p-4 grid border border-white gap-5;
}
</style>