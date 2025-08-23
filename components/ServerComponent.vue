<template>
    <div class="html bg-black">
        <div class="text-white" :class="{ 'bg-green-500': clicks > 0 }">
            Header
        </div>

        <div class="grid-cols-2">
            <div class="bg-black-500 text-white">
                Sidebar
            </div>

            <div class="bg-black-500 text-white border-none !p-0">
                <div>
                    Main content component
                    
                    <div :class="{ 'bg-green-500': clicks > 0 }">
                        Carousel
                            <span class="flex gap-px text-xs justify-between">

                        <div class="bg-black !p-0 items-center">
                            Item 1
                        </div>
                        <div class="bg-black !p-0 items-center">
                            Item 2
                        </div>
                        <div class="bg-black !p-0 items-center">
                            Item 3
                        </div>

                        </span>
                    </div>
                </div>
                <div :class="{ 'bg-green-500': clicks > 0 }">
                    Carousel
                </div>
            </div>
        </div>

        <div class="bg-black-500 text-white">
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
    @apply p-2 grid border border-white gap-5;
}
</style>