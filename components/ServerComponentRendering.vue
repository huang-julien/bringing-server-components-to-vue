<template>
  <div>
    <div class="grid grid-cols-3 gap-2">
        <template v-for="(step, index) in steps">
        <div  v-if="clicks >= index" class="flex flex-col items-center text-center p-2 border rounded-lg">
            <span class="text-3xl mb">
                <Icon :icon="step.icon" />
            </span>
            <p class="font-semibold text-xs">{{ step.title }}</p>
        </div></template>
    </div>
  </div>
</template>


<script lang="ts" setup>
import { Icon } from "@iconify/vue";

import { useSlideContext } from '@slidev/client'

const steps = [
    {
        icon: 'material-symbols:person',
        title: 'Incoming user request'
    },
    {
        icon: 'logos:vue',
        title: 'Renders the component server side'
    },
    {
        icon: 'dashicons:html',
        title: 'Renders to AST'
    },
    {
        icon: 'streamline-freehand:programming-language-browser-html',
        title: 'Browser receives the response'
    },
    {
        icon: 'logos:vue',
        title: 'Renders the component from AST'
    },
]


const clicks = useSlideContext().$clicks
</script>