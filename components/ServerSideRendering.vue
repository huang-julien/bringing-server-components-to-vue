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
        title: 'App created server side'
    },
    {
        icon: 'dashicons:html',
        title: 'Render app to string'
    },
    {
        icon: 'streamline-freehand:programming-language-browser-html',
        title: 'Browser receive HTML response'
    },
    {
        icon: 'logos:vue',
        title: 'App created client side'
    },
    {
        icon: 'devicon:javascript',
        title: 'Hydrate app on client'
    }
]


const clicks = useSlideContext().$clicks
</script>