<template>
  <div>
    <div class="grid grid-cols-3 gap-4">
        <template v-for="(step, index) in steps">
        <div v-show="clicks >= index" class="flex flex-col items-center text-center p-2 border rounded-lg">
            <span class="text-xl">
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
        title: 'Renders the component'
    },
    {
        icon: 'dashicons:html',
        title: 'Serialize to AST'
    },
    {
        icon: 'streamline-freehand:programming-language-browser-html',
        title: 'Browser receives the response'
    },
    {
        icon: 'logos:vue',
        title: 'Renders  from AST'
    },
]


const clicks = useSlideContext().$clicks
</script>