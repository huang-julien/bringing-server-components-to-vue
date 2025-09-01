---
# You can ./golden-navy start simply with 'default'
theme: ./gold-navy
# random image from a curated Unsplash collection by Anthony
# like them? see https://unsplash.com/collections/94734566/slidev
background: https://cover.sli.dev
# some information about your slides (markdown enabled)
title: Bringing server components to vue
info: |
  ## Slidev Starter Template
  Presentation slides for developers.

  Learn more at [Sli.dev](https://sli.dev)
# apply unocss classes to the current slide
class: text-center
# https://sli.dev/features/drawing
drawings:
  persist: false
# slide transition: https://sli.dev/guide/animations.html#slide-transitions
transition: slide-left
# enable MDC Syntax: https://sli.dev/features/mdc
mdc: true
# open graph
seoMeta:
  # By default, Slidev will use ./og-image.png if it exists,
  # or generate one from the first slide if not found.
  ogImage: auto
  # ogImage: https://cover.sli.dev
---

# Bringing server components to Vue
 
---

<div class="flex flex-col mx-auto text-center">

# Hello 👋 I'm Julien

### I'm currently the frontend technical lead at Leetchi

<img v-drag="[36,171,225,225]" src="/assets/pfp.jpg" class="rounded-full" />
 

<div>

Maintainer of:  

nuxt/nuxt  
@nuxt/scripts  

Creator of:  

nitro-opentelemetry  
nitro-applicationinsights  
nuxt-applicationinsights  
vue-onigiri

</div>

<div>
<logos-github-icon /> @huang-julien
</div>
<div>
<logos-bluesky /> julienhuang-dev.bsky.social
</div>
<div>
<logos-linkedin-icon /> julien-huang
</div>
</div>


---
layout: intro
---

# What is a server component ?

---

# Components in frontend frameworks

<div class="grid gap-4 grid-cols-[_2fr_1fr]">

<div>

- Self contained
- Reusable UI unit
- Hold local state

<two-cols class="gap-4">

```tsx
export default function Gallery() {
  return (
    <section>
      <h1>Amazing scientists</h1>
      <Profile />
      <Profile />
      <Profile />
    </section>
  );
}
```

```html
<script setup>
import { ref } from 'vue'

const msg = ref('Hello World!')
</script>

<template>
  <h1>{{ msg }}</h1>
  <input v-model="msg" />
</template>

<style>
h1 { color: blanchedalmond; }
</style>
```

</two-cols>

</div>
<div>


<div class="flex gap-4 my-5">

<logos-vue size="2rem" />

<devicon-angular size="2rem" />

<devicon-react size="2rem"  />

<logos-svelte-icon size="2rem" />

</div>

<img src="/assets/frameworks-everywhere.jpg" />
</div>

</div>

<!-- 

A component in modern frontend frameworks is a self-contained, reusable piece of UI that encapsulates structure, appearance, behavior, and state.

-->


---

# Meta-frameworks

<div class="flex gap-4 my-5">

<logos-nuxt-icon size="2rem" />

<devicon-nextjs size="2rem" />

<devicon-astro size="2rem"  />

<logos-analog  size="2rem" />

</div>

::two-cols

<div>

- Built on top of frontend frameworks
- Set a bridge between frontend and backend development
- Allows new capabilities for frontend frameworks such as SSR, SSG
- Bring a whole new set of DX feature by abstracting a lot of concepts
 
</div>

<img src="/assets/meta-frameworks-meme.png" class="w-3/4" />

::

---
clicks: 5
---

# How your app are rendered with SSR meta frameworks

<ServerSideRendering />

---
clicks: 4
---

# Server components

::two-cols

<div>

- server only
- zero JS shipped/loaded to your browser
- fully isolated
- non interactive

<ServerComponentRendering />

</div>

<img v-drag="[613,76,319,425]" src="/assets/servercomponent_meme.jpg" />

::

---

# Benefits

- Performances
- Faster initial load
- Direct access to backend resources

::window{filename="components/ServerComponent.vue"}
```html
<template>
    <div class="my-auto h-full">
        <ContentRenderer v-if="content" :value="content" />
    </div>
</template>

<script setup lang="ts">
// tooooonnnss of javascript
import { ContentRenderer } from "markdown-renderer"

const props = defineProps<{ id: string }>()

// huuuuuuuuge payload
const data = await fetch(`some-url.com?id=id`)
  .then((r) => r.json())
</script>
```
::

---
layout: intro
---

# Server components can make developpement more complex

---

## Where code runs

<two-cols gap-4>

::window{filename="components/FollowButton.vue"}

```html
<script setup lang="ts">
const props = defineProps<{ userId: string }>()
const following = ref(false)
// interactivity expected from browser interaction
function toggle() { following.value = !following.value }
</script>

<template>
  <button @click="toggle">
    {{ following ? 'Unfollow' : 'Follow' }}
  </button>
</template>
```
::

::window{filename="components/UserList.server.vue"}

````md magic-move
```html
<script setup lang="ts">
// Runs only server side, rendered statically
const { data: users } = await useAsyncData('users', () => $fetch('/api/users'))
</script>

<template>
  <ul>
    <li v-for="u in users" :key="u.id">
      {{ u.name }}
      <FollowButton :user-id="u.id" />
    </li>
  </ul>
</template>
```

```html
<script setup lang="ts">
// Runs only server side, rendered statically
const { data: users } = await useAsyncData('users', () => $fetch('/api/users'))
</script>

<template>
  <ul>
    <li v-for="u in users" :key="u.id">
      {{ u.name }}
      <ClientOnly>
        <FollowButton :user-id="u.id" />
      </ClientOnly>
    </li>
  </ul>
</template>
```

```html
<script setup lang="ts">
// Runs only server side, rendered statically
const { data: users } = await useAsyncData('users', () => $fetch('/api/users'))
</script>

<template>
  <ul>
    <li v-for="u in users" :key="u.id">
      {{ u.name }}
      <FollowButton v-load-client :user-id="u.id" />
    </li>
  </ul>
</template>
```
````
::

</two-cols>

---

## Pure vs effect

::window{filename="components/UserList.server.vue"}
```html
<script setup lang="ts">
import { onMounted, ref } from 'vue'
const now = ref(new Date())
onMounted(() => {
  // Browser-only API and timers are effects — this won’t run on server
  setInterval(() => { now.value = new Date() }, 1000)
})
</script>

<template>
  <time>{{ now.toLocaleTimeString() }}</time>
</template>
```
::

---

## Isolation

::window{filename="components/User.server.vue"}

```html
<script setup lang="ts">
import { authStore } from "@/stores"
// This runs server only and without your browser context !
const auth = useAuthStore()
</script>

<template>
  <div>
    {{ auth.username}}
  </div>
</template>
```

::

---

## Poor component design

```bash{all|3|4|5|6}
|- App
  |- client component
  |- server component
    |- client component
      |- server component
        |- client component
  |- client component
```

---

# Does Vue also provide server components ?

<img class="rounded-xl mx-auto" src="/assets/vsc.png" />

---

# And what about Nuxt ?

- Allows server side rendering
- Allows pre-rendering your app

<img v-drag="[618,74,275,413]" src="/assets/nuxtisland.png" />

---
layout: intro
---

# `<NuxtIsland>`

<img src="/assets/island_scene.svg" />

---

# How to use NuxtIsland


::window{filename="components/MyContent.vue"}
```html
<template>
    <div class="my-auto h-full">
        <ContentRenderer v-if="content" :value="content" />
    </div>
</template>

<script setup lang="ts">
const props = defineProps<{
  id: string
}>()
const { data: content } = await useAsyncData(computed(() => id),() => queryCollection('content').path(route.path).first())
</script>
```
::

<Spacer />

::window{filename="pages/index.vue"}

```html
<template>
  <div>
    <NuxtIsland name="MyContent" :props="{id: 'some-id'}" />
  </div>
</template>
```

::

---

# Pure HTML brings a lot of limitations

::two-cols

````js
{
  "id": "OPlPaWtESbsHmiL1DheP8Ny7Fu9YaBs2YJqXaFyHY",
  "head": {
    "link": [
      {
        "rel": "stylesheet",
        "href": "/_nuxt/components/islands/PureComponent.vue?vue&type=style&index=0&scoped=c0c0cf89&lang.css",
        "crossorigin": ""
      }
    ]
  },
  "html": `
  <div data-island-uid data-v-c0c0cf89>
    Was router enabled: true 
    <br data-v-c0c0cf89> 
    Props: 
    <pre data-v-c0c0cf89>{\n  \"number\": 100,\n  \"str\": \"hello world\",\n  \"obj\": {\n    \"json\": \"works\"\n  },\n  \"bool\": true\n}
    </pre>
  </div>
  `
}
````

<img v-drag="[550,102,349,391]" src="/assets/island_workaround.jpg" />

::

---

# Overcomplexity due to workarounds

<Window filename="packages\nuxt\src\app\components\nuxt-island.ts" >

<div class="h-[45vh]">

````ts
import type { Component, PropType, RendererNode, VNode } from 'vue'
import { Fragment, Teleport, computed, createStaticVNode, createVNode, defineComponent, getCurrentInstance, h, nextTick, onBeforeUnmount, onMounted, ref, shallowRef, toRaw, watch, withMemo } from 'vue'
import { debounce } from 'perfect-debounce'
import { hash } from 'ohash'
import { appendResponseHeader } from 'h3'
import type { ActiveHeadEntry, SerializableHead } from '@unhead/vue'
import { randomUUID } from 'uncrypto'
import { joinURL, withQuery } from 'ufo'
import type { FetchResponse } from 'ofetch'

import type { NuxtIslandResponse } from '../types'
import { useNuxtApp, useRuntimeConfig } from '../nuxt'
import { prerenderRoutes, useRequestEvent } from '../composables/ssr'
import { injectHead } from '../composables/head'
import { getFragmentHTML, isEndFragment, isStartFragment } from './utils'

// @ts-expect-error virtual file
import { appBaseURL, remoteComponentIslands, selectiveClient } from '#build/nuxt.config.mjs'

const pKey = '_islandPromises'
const SSR_UID_RE = /data-island-uid="([^"]*)"/
const DATA_ISLAND_UID_RE = /data-island-uid(="")?(?!="[^"])/g
const SLOTNAME_RE = /data-island-slot="([^"]*)"/g
const SLOT_FALLBACK_RE = / data-island-slot="([^"]*)"[^>]*>/g
const ISLAND_SCOPE_ID_RE = /^<[^> ]*/

let id = 1
const getId = import.meta.client ? () => (id++).toString() : randomUUID

const components = import.meta.client ? new Map<string, Component>() : undefined

async function loadComponents (source = appBaseURL, paths: NuxtIslandResponse['components']) {
  if (!paths) { return }

  const promises: Array<Promise<void>> = []

  for (const [component, item] of Object.entries(paths)) {
    if (!(components!.has(component))) {
      promises.push((async () => {
        const chunkSource = joinURL(source, item.chunk)
        const c = await import(/* @vite-ignore */ chunkSource).then(m => m.default || m)
        components!.set(component, c)
      })())
    }
  }

  await Promise.all(promises)
}

export default defineComponent({
  name: 'NuxtIsland',
  inheritAttrs: false,
  props: {
    name: {
      type: String,
      required: true,
    },
    lazy: Boolean,
    props: {
      type: Object,
      default: () => undefined,
    },
    context: {
      type: Object,
      default: () => ({}),
    },
    scopeId: {
      type: String as PropType<string | undefined | null>,
      default: () => undefined,
    },
    source: {
      type: String,
      default: () => undefined,
    },
    dangerouslyLoadClientComponents: {
      type: Boolean,
      default: false,
    },
  },
  emits: ['error'],
  async setup (props, { slots, expose, emit }) {
    let canTeleport = import.meta.server
    const teleportKey = shallowRef(0)
    const key = shallowRef(0)
    const canLoadClientComponent = computed(() => selectiveClient && (props.dangerouslyLoadClientComponents || !props.source))
    const error = ref<unknown>(null)
    const config = useRuntimeConfig()
    const nuxtApp = useNuxtApp()
    const filteredProps = computed(() => props.props ? Object.fromEntries(Object.entries(props.props).filter(([key]) => !key.startsWith('data-v-'))) : {})
    const hashId = computed(() => hash([props.name, filteredProps.value, props.context, props.source]).replace(/[-_]/g, ''))
    const instance = getCurrentInstance()!
    const event = useRequestEvent()

    let activeHead: ActiveHeadEntry<SerializableHead>

    // TODO: remove use of `$fetch.raw` when nitro 503 issues on windows dev server are resolved
    const eventFetch = import.meta.server ? event!.fetch : import.meta.dev ? $fetch.raw : globalThis.fetch
    const mounted = shallowRef(false)
    onMounted(() => { mounted.value = true; teleportKey.value++ })
    onBeforeUnmount(() => { if (activeHead) { activeHead.dispose() } })
    function setPayload (key: string, result: NuxtIslandResponse) {
      const toRevive: Partial<NuxtIslandResponse> = {}
      if (result.props) { toRevive.props = result.props }
      if (result.slots) { toRevive.slots = result.slots }
      if (result.components) { toRevive.components = result.components }
      if (result.head) { toRevive.head = result.head }
      nuxtApp.payload.data[key] = {
        __nuxt_island: {
          key,
          ...(import.meta.server && import.meta.prerender)
            ? {}
            : { params: { ...props.context, props: props.props ? JSON.stringify(props.props) : undefined } },
          result: toRevive,
        },
        ...result,
      }
    }

    const payloads: Partial<Pick<NuxtIslandResponse, 'slots' | 'components'>> = {}

    if (instance.vnode.el) {
      const slots = toRaw(nuxtApp.payload.data[`${props.name}_${hashId.value}`])?.slots
      if (slots) { payloads.slots = slots }
      if (selectiveClient) {
        const components = toRaw(nuxtApp.payload.data[`${props.name}_${hashId.value}`])?.components
        if (components) { payloads.components = components }
      }
    }

    const ssrHTML = ref<string>('')

    if (import.meta.client && instance.vnode?.el) {
      if (import.meta.dev) {
        let currentEl = instance.vnode.el
        let startEl: RendererNode | null = null
        let isFirstElement = true

        while (currentEl) {
          if (isEndFragment(currentEl)) {
            if (startEl !== currentEl.previousSibling) {
              console.warn(`[\`Server components(and islands)\`] "${props.name}" must have a single root element. (HTML comments are considered elements as well.)`)
            }
            break
          } else if (!isStartFragment(currentEl) && isFirstElement) {
            // find first non-comment node
            isFirstElement = false
            if (currentEl.nodeType === 1) {
              startEl = currentEl
            }
          }
          currentEl = currentEl.nextSibling
        }
      }
      ssrHTML.value = getFragmentHTML(instance.vnode.el, true)?.join('') || ''
      const key = `${props.name}_${hashId.value}`
      nuxtApp.payload.data[key] ||= {}
      // clear all data-island-uid to avoid conflicts when saving into payloads
      nuxtApp.payload.data[key].html = ssrHTML.value.replaceAll(new RegExp(`data-island-uid="${ssrHTML.value.match(SSR_UID_RE)?.[1] || ''}"`, 'g'), `data-island-uid=""`)
    }

    const uid = ref<string>(ssrHTML.value.match(SSR_UID_RE)?.[1] || getId())

    const currentSlots = new Set(Object.keys(slots))
    const availableSlots = computed(() => new Set([...ssrHTML.value.matchAll(SLOTNAME_RE)].map(m => m[1])))
    const html = computed(() => {
      let html = ssrHTML.value

      if (props.scopeId) {
        html = html.replace(ISLAND_SCOPE_ID_RE, full => full + ' ' + props.scopeId)
      }

      if (import.meta.client && !canLoadClientComponent.value) {
        for (const [key, value] of Object.entries(payloads.components || {})) {
          html = html.replace(new RegExp(` data-island-uid="${uid.value}" data-island-component="${key}"[^>]*>`), (full) => {
            return full + value.html
          })
        }
      }

      if (payloads.slots) {
        return html.replaceAll(SLOT_FALLBACK_RE, (full, slotName) => {
          if (!currentSlots.has(slotName)) {
            return full + (payloads.slots?.[slotName]?.fallback || '')
          }
          return full
        })
      }
      return html
    })

    const head = injectHead()

    async function _fetchComponent (force = false) {
      const key = `${props.name}_${hashId.value}`

      if (!force && nuxtApp.payload.data[key]?.html) { return nuxtApp.payload.data[key] }

      const url = remoteComponentIslands && props.source ? joinURL(props.source, `/__nuxt_island/${key}.json`) : `/__nuxt_island/${key}.json`
      if (import.meta.server && import.meta.prerender) {
        // Hint to Nitro to prerender the island component
        nuxtApp.runWithContext(() => prerenderRoutes(url))
      }
      // TODO: Validate response
      // $fetch handles the app.baseURL in dev
      const r = await eventFetch(withQuery(((import.meta.dev && import.meta.client) || props.source) ? url : joinURL(config.app.baseURL ?? '', url), {
        ...props.context,
        props: props.props ? JSON.stringify(props.props) : undefined,
      }))
      try {
        const result = import.meta.server || !import.meta.dev ? await r.json() : (r as FetchResponse<NuxtIslandResponse>)._data
        // TODO: support passing on more headers
        if (import.meta.server && import.meta.prerender) {
          const hints = r.headers.get('x-nitro-prerender')
          if (hints) {
            appendResponseHeader(event!, 'x-nitro-prerender', hints)
          }
        }
        setPayload(key, result)
        return result
      } catch (e: any) {
        if (r.status !== 200) {
          throw new Error(e.toString(), { cause: r })
        }
        throw e
      }
    }

    async function fetchComponent (force = false) {
      nuxtApp[pKey] ||= {}
      nuxtApp[pKey][uid.value] ||= _fetchComponent(force).finally(() => {
        delete nuxtApp[pKey]![uid.value]
      })
      try {
        const res: NuxtIslandResponse = await nuxtApp[pKey][uid.value]

        ssrHTML.value = res.html.replaceAll(DATA_ISLAND_UID_RE, `data-island-uid="${uid.value}"`)
        key.value++
        error.value = null
        payloads.slots = res.slots || {}
        payloads.components = res.components || {}

        if (selectiveClient && import.meta.client) {
          if (canLoadClientComponent.value && res.components) {
            await loadComponents(props.source, res.components)
          }
        }

        if (res?.head) {
          if (activeHead) {
            activeHead.patch(res.head)
          } else {
            activeHead = head.push(res.head)
          }
        }

        if (import.meta.client) {
          // must await next tick for Teleport to work correctly with static node re-rendering
          nextTick(() => {
            canTeleport = true
            teleportKey.value++
          })
        }
      } catch (e) {
        error.value = e
        emit('error', e)
      }
    }

    expose({
      refresh: () => fetchComponent(true),
    })

    if (import.meta.hot) {
      import.meta.hot.on(`nuxt-server-component:${props.name}`, () => {
        fetchComponent(true)
      })
    }

    if (import.meta.client) {
      watch(props, debounce(() => fetchComponent(), 100), { deep: true })
    }

    if (import.meta.client && !instance.vnode.el && props.lazy) {
      fetchComponent()
    } else if (import.meta.server || !instance.vnode.el || !nuxtApp.payload.serverRendered) {
      await fetchComponent()
    } else if (selectiveClient && canLoadClientComponent.value) {
      await loadComponents(props.source, payloads.components)
    }

    return (_ctx: any, _cache: any) => {
      if (!html.value || error.value) {
        return [slots.fallback?.({ error: error.value }) ?? createVNode('div')]
      }
      return [
        withMemo([key.value], () => {
          return createVNode(Fragment, { key: key.value }, [h(createStaticVNode(html.value || '<div></div>', 1))])
        }, _cache, 0),

        // should away be triggered ONE tick after re-rendering the static node
        withMemo([teleportKey.value], () => {
          const teleports: Array<VNode> = []
          // this is used to force trigger Teleport when vue makes the diff between old and new node
          const isKeyOdd = teleportKey.value === 0 || !!(teleportKey.value && !(teleportKey.value % 2))

          if (uid.value && html.value && (import.meta.server || props.lazy ? canTeleport : (mounted.value || instance.vnode?.el))) {
            for (const slot in slots) {
              if (availableSlots.value.has(slot)) {
                teleports.push(createVNode(Teleport,
                  // use different selectors for even and odd teleportKey to force trigger the teleport
                  { to: import.meta.client ? `${isKeyOdd ? 'div' : ''}[data-island-uid="${uid.value}"][data-island-slot="${slot}"]` : `uid=${uid.value};slot=${slot}` },
                  { default: () => (payloads.slots?.[slot]?.props?.length ? payloads.slots[slot].props : [{}]).map((data: any) => slots[slot]?.(data)) }),
                )
              }
            }
            if (selectiveClient) {
              if (import.meta.server) {
                if (payloads.components) {
                  for (const [id, info] of Object.entries(payloads.components)) {
                    const { html, slots } = info
                    let replaced = html.replaceAll('data-island-uid', `data-island-uid="${uid.value}"`)
                    for (const slot in slots) {
                      replaced = replaced.replaceAll(`data-island-slot="${slot}">`, full => full + slots[slot])
                    }
                    teleports.push(createVNode(Teleport, { to: `uid=${uid.value};client=${id}` }, {
                      default: () => [createStaticVNode(replaced, 1)],
                    }))
                  }
                }
              } else if (canLoadClientComponent.value && payloads.components) {
                for (const [id, info] of Object.entries(payloads.components)) {
                  const { props, slots } = info
                  const component = components!.get(id)!
                  // use different selectors for even and odd teleportKey to force trigger the teleport
                  const vnode = createVNode(Teleport, { to: `${isKeyOdd ? 'div' : ''}[data-island-uid='${uid.value}'][data-island-component="${id}"]` }, {
                    default: () => {
                      return [h(component, props, Object.fromEntries(Object.entries(slots || {}).map(([k, v]) => ([k, () => createStaticVNode(`<div style="display: contents" data-island-uid data-island-slot="${k}">${v}</div>`, 1),
                      ]))))]
                    },
                  })
                  teleports.push(vnode)
                }
              }
            }
          }

          return h(Fragment, teleports)
        }, _cache, 1),
      ]
    }
  },
})

````

</div>

</Window>

---

# A workaround I'm crying each time i'm working on it

<v-click>
<img src="/assets/mypain_islandrenderingissue.png" class="w-1/2 mx-auto" />
</v-click>

---

<div class="overflow-auto h-fit">

````md magic-move {class:'h-50vh overflow-auto'}

```js
[
  {
    type: 'static-vnode',
    html: `
      <div data-island-uid="some-uid">
        this is a server component
        <div 
          style="display: contents;"
          data-island-uid="34ddcd4d-6659-4df9-8f14-bfff83699976" 
          data-island-component="v-0-0-0"
        >
          <div class="sugar-counter">
            Sugar Counter 12 x 1 = 12 <button> Inc </button>
          </div>
        </div>
        <div 
          style="display: contents;" 
          data-island-uid="17868edc-8f2a-43d9-86a8-0fc49e82d07b"
          data-island-slot="default"
        >
        </div>
      </div>
    `
  }
]
```

```js
[
  {
    type: 'static-vnode',
    html: `
      <div data-island-uid="some-uid">
        this is a server component
        <div 
          style="display: contents;"
          data-island-uid="34ddcd4d-6659-4df9-8f14-bfff83699976" 
          data-island-component="v-0-0-0"
        >
          <div class="sugar-counter">
            Sugar Counter 12 x 1 = 12 <button> Inc </button>
          </div>
        </div>
        <div 
          style="display: contents;" 
          data-island-uid="17868edc-8f2a-43d9-86a8-0fc49e82d07b"
          data-island-slot="default"
        >
        </div>
      </div>
    `
  },
  {
    type: 'Teleport',
    target: '[data-island-uid="17868edc-8f2a-43d9-86a8-0fc49e82d07b"][data-island-slot="default"]',
    children: [
      {
        // ...
      }
    ]
  },
  {
    type: 'Teleport',
    target: '[data-island-uid="17868edc-8f2a-43d9-86a8-0fc49e82d07b"][data-island-component="v-0-0-0"]',
    children: [
      {
        // ...
      }
    ]
  },
]
```

```js
[
  {
    type: 'static-vnode',
    html: `
      <div data-island-uid="some-uid">
        this is a server component
        <div 
          style="display: contents;"
          data-island-uid="34ddcd4d-6659-4df9-8f14-bfff83699976" 
          data-island-component="v-0-0-0"
        >
          <div class="sugar-counter">
            Sugar Counter 12 x 1 = 12 <button> Inc </button>
          </div>
        </div>
        <div 
          style="display: contents;" 
          data-island-uid="17868edc-8f2a-43d9-86a8-0fc49e82d07b"
          data-island-slot="default"
        >
        </div>
      </div>
    `
  },
  {
    type: 'Teleport',
    target: 'div[data-island-uid="17868edc-8f2a-43d9-86a8-0fc49e82d07b"][data-island-slot="default"]',
    children: [
      {
        // ...
      }
    ]
  },
  {
    type: 'Teleport',
    target: 'div[data-island-uid="17868edc-8f2a-43d9-86a8-0fc49e82d07b"][data-island-component="v-0-0-0"]',
    children: [
      {
        // ...
      }
    ]
  },
]
```
```ts
[
  {
    type: 'static-vnode',
    html: `
      <div data-island-uid="some-uid">
        this is a server component
        <div 
          style="display: contents;"
          data-island-uid="34ddcd4d-6659-4df9-8f14-bfff83699976" 
          data-island-component="v-0-0-0"
        >
          <div class="sugar-counter">
            Sugar Counter 12 x 1 = 12 <button> Inc </button>
          </div>
        </div>
        <div 
          style="display: contents;" 
          data-island-uid="17868edc-8f2a-43d9-86a8-0fc49e82d07b"
          data-island-slot="default"
        >
        </div>
      </div>
    `
  },
  {
    type: 'Teleport',
    target: '[data-island-uid="17868edc-8f2a-43d9-86a8-0fc49e82d07b"][data-island-slot="default"]',
    children: [
      {
        // ...
      }
    ]
  },
  {
    type: 'Teleport',
    target: '[data-island-uid="17868edc-8f2a-43d9-86a8-0fc49e82d07b"][data-island-component="v-0-0-0"]',
    children: [
      {
        // ...
      }
    ]
  },
]
```

````

</div>


---
layout: intro
---

# Bringing server components to Vue

<img v-drag="[291,413,375,375]" src="/assets/vue-onigiri.png" />

---
layout: intro
---

# vue-onigiri

<img v-drag="[309,312,383,216]"  src="/assets/onigiri.jpg" />

<img v-drag="[353,-1,276,276]" src="/assets/vue-onigiri.png" />

---

# What does it do ?

<v-clicks>

- Serialize VNodes
- Emit components chunks that returns vnode for server bundle
- that's... all

</v-clicks>

<img v-click v-drag="[283,195,448,284]" src="/assets/itaintmuch.png" />

---
layout: intro
---

# Introducing AST for Vue

---

::two-cols{class="gap-4"}

<div>

```ts
const enum VServerComponentType {
  Element,
  Component,
  Text,
  Fragment,
  Suspense,
}
```

````md magic-move

```html
  <div>
    <div>1</div>
    <div>2</div>
  </div>
```

```html
  <div>
    <div>1</div>
    <div>2</div>
    <Counter />
  </div>
```

```html
  <div>
    <div>1</div>
    <div>2</div>
    <Counter v-load-client>
    </Counter>
  </div>
```

```html
  <div>
    <Suspense>
      <Counter v-load-client>
        <div>
          Hello VueJS Paris
        </div>
      </Counter>
    </Suspense>
  </div>
```

````

````md magic-move{at:'0'}
```ts
type VServerComponentElement = [
  VServerComponentType.Element,
  Tag,
  Attrs,
  Children,
]
```
```ts
type VServerComponentComponent = [
  VServerComponentType.Component,
  Props,
  // client chunk location
  ChunkPath,
  // export name
  string,
  Slots,
]
```
```ts
type VServerComponentComponent = [
  VServerComponentType.Component,
  Props,
  // client chunk location
  ChunkPath,
  // export name
  string,
  Slots,
]
```
```ts
type VServerComponentComponent = [
  VServerComponentType.Component,
  Props,
  // client chunk location
  ChunkPath,
  // export name
  string,
  Slots,
]
```
```ts
type VServerComponentSuspense = [
  VServerComponentType.Suspense,
  VServerComponent[] | undefined,
];
```
````

</div>

````md magic-move{at:'0', class:'overflow-hidden'}

```ts
const ast = [
  VServerComponentType.Element,
  'div',
  null,
  [
    [VServerComponentType.Element, 'div', null, '1'],
    [VServerComponentType.Element, 'div', null, '2'],
  ]
]
```

```ts
const ast = [
  VServerComponentType.Element,
  'div',
  null,
  [
    [VServerComponentType.Element, 'div', null, '1'],
    [VServerComponentType.Element, 'div', null, '2'],
    [
      VServerComponentType.Element,
      'div',
      null,
      [VServerComponentType.Text, 'Counter: 0'],
      [
        VServerComponentType.Element, 
        'button', 
        null, 
        [[VServerComponentType.Text, 'Increment']]],
    ]
  ]
]
```

```ts
const ast = [
  VServerComponentType.Element,
  'div',
  null,
  [
    [VServerComponentType.Element, 'div', null, '1'],
    [VServerComponentType.Element, 'div', null, '2'],
    [
      VServerComponentType.Component,
      null,
      "/test/fixtures/components/Counter.vue",
      "default",
      null
    ]
  ]
]
```

```ts
const ast = [
  VServerComponentType.Element,
  'div',
  null,
  [
    [VServerComponentType.Element, 'div', null, '1'],
    [VServerComponentType.Element, 'div', null, '2'],
    [
      VServerComponentType.Component,
      null,
      "/test/fixtures/components/Counter.vue",
      "default",
      {
        default: [
          VServerComponentType.Element, 
          'div', 
          null, 
          'Hello VueJS Paris'
        ]
      }
    ]
  ]
]
```
```ts
const ast = [
  VServerComponentType.Element,
  'div',
  null,
  [
    [
      VServerComponentType.Suspense,
      null,
      [
        [
          VServerComponentType.Component,
          null,
          "/test/fixtures/components/Counter.vue",
          "default",
          {
            default: [
              VServerComponentType.Element, 
              'div', 
              null, 
              'Hello VueJS Paris'
            ]
          }
        ]
      ]
    ]
  ]
]
```

````

::


---

# Applying it to NuxtIsland

```ts
interface NuxtIslandResponse {
  id?: string
  html: string
  head: Head
  props?: Record<string, Record<string, any>>
  components?: Record<string, NuxtIslandClientResponse>
  slots?: Record<string, NuxtIslandSlotResponse>
}
```

```ts twoslash
// ---cut-start---
export const enum VServerComponentType {
  Element,
  Component,
  Text,
  Fragment,
  Suspense,
}
type Tag = string;
type ChunkPath = string;
type Children = VServerComponent | undefined;
type Props = Record<string, any> | undefined;
type Attrs = Record<string, any> | undefined;
type Slots = Record<string, Children> | undefined;
type VServerComponentElement = [
  VServerComponentType.Element,
  Tag,
  Attrs,
  Children,
];

export type VServerComponentComponent = [
  VServerComponentType.Component,
  Props,
  ChunkPath,
  // export name
  string,
  Slots,
];
type VServerComponentText = [VServerComponentType.Text, string];
type VServerComponentFragment = [VServerComponentType.Fragment, Children[]];
type VServerComponentSuspense = [
  VServerComponentType.Suspense,
  VServerComponent[] | undefined,
];

type MaybePromise<T> = T | Promise<T>;

type VServerComponentElementBuffered = [
  VServerComponentType.Element,
  Tag,
  Attrs,
  MaybePromise<(VServerComponentBuffered | undefined)[]> | undefined,
];

type VServerComponentComponentBuffered = [
  VServerComponentType.Component,
  Props,
  ChunkPath,
  // export name
  string,
  MaybePromise<Record<string, VServerComponent[] | undefined>> | undefined,
];
type VServerComponentTextBuffered = [VServerComponentType.Text, string];
type VServerComponentFragmentBuffered = [
  VServerComponentType.Fragment,
  MaybePromise<VServerComponentBuffered[]> | undefined,
];
type VServerComponentSuspenseBuffered = [
  VServerComponentType.Suspense,
  MaybePromise<VServerComponentBuffered[]> | undefined,
];

export type VServerComponentBuffered =
  | VServerComponentElementBuffered
  | VServerComponentComponentBuffered
  | VServerComponentTextBuffered
  | VServerComponentFragmentBuffered
  | VServerComponentSuspenseBuffered;

export type VServerComponent =
  | VServerComponentElement
  | VServerComponentComponent
  | VServerComponentText
  | VServerComponentFragment
  | VServerComponentSuspense;
  interface NuxtIslandSlotResponse {
  props: Array<unknown>
  fallback?: string
}

interface NuxtIslandClientResponse {
  html: string
  props: unknown
  chunk: string
  slots?: Record<string, string>
}
type Head = any
// ---cut-end---
interface NuxtIslandResponse {
  id?: string
  ast: VServerComponent
  head: Head
  props?: Record<string, Record<string, any>>
  components?: Record<string, NuxtIslandClientResponse>
  slots?: Record<string, NuxtIslandSlotResponse>
}
```

---

# What about Vapor mode ?

---

# Future ideas

- Create a playground similar to Vue SFC Playground
- Compilation based AST render function
