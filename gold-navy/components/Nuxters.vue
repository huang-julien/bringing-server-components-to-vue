<template>
    <div class="">
        <div class="fixed  top-0 left-0 right-0 bottom-0 flex gap-1 flex-wrap p-10">
            <NuxtersPicture v-for="nuxter in data.splice(0, 150)" class="w-14 h-14 rounded-full"
                :src="`https://avatars.githubusercontent.com/u/${nuxter.githubId}?s=80`" />

        </div>

        <div v-click class="relative flex z-10">
            <div class="bg-navy mt-50 flex p-10 mx-auto rounded-lg text-yellow text-2xl">
                + {{ data.length }} contributeurs
            </div>
        </div>

    </div>
</template>

<script setup lang="ts">
import { $fetch } from 'ofetch'

interface Contributor {
    username: string
    githubId: string
}

const data = await $fetch<Contributor[]>('https://api.nuxt.com/contributors').catch(() => [])
</script>