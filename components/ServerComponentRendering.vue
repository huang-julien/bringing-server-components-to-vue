<template>
  <div>
    <div class="grid grid-cols-3 gap-2">
        <template v-for="(step, index) in steps">
        <div  v-if="clicks >= index" class="flex flex-col items-center text-center p-4 border rounded-lg">
            <span class="text-4xl mb-2">
                <Icon :icon="step.icon" />
            </span>
            <h3 class="font-semibold">{{ step.title }}</h3>
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