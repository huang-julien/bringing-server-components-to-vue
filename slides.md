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

### Frontend technical lead at Leetchi
### Nuxt core team member

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

::window{filename="components/UserList.server.vue"}

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
::

::window{filename="components/FollowButton.vue"}

````md magic-move
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
````
::

</two-cols>

---

## Effect and reactivity free components

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

````md magic-move{at:'2'}

```bash
- Request
```

```bash
- Request
- Import JS
```

```bash
- Request
- Import JS
- Request
```

```bash
- Request
- Import JS
- Request
- Import JS
```

````

---

# Does Vue also provide server components ?

<img class="rounded-xl mx-auto" src="/assets/vsc.png" />

---


# And what about Nuxt ?

<v-clicks>

- THE meta-framework around VueJS
- Powered by Nitro and UNJS
- Allows server side rendering
- Allows pre-rendering your app
- VueJS application runs server side and client side

</v-clicks> 

<img v-drag="[618,74,275,413]" src="/assets/nuxtisland.png" />

---
layout: intro
---

# `<NuxtIsland>`

<img src="/assets/island_scene.svg" />

---
clicks: 1
---

::two-cols{class="gap-5"}

<NuxtIsland />

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

::

---

# How to use NuxtIsland


::window{filename="components/MyContent.server.vue | components/island/MyContent.server.vue"}
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
    <MyContent id="some-id" />
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

<img v-drag="[550,102,349,391]" v-click src="/assets/island_workaround.jpg" />

::

---
layout: intro
---

# A workaround I'm crying each time i'm working on it

<v-click>
<img src="/assets/mypain_islandrenderingissue.png" class="w-1/2 mx-auto" />
</v-click>

---

::window

<div class="max-h-[50vh] overflow-auto">

```ts
(_ctx: any, _cache: any) => {
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
```

</div>

::

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

<img v-drag="[291,413,375,375]" src="/assets/vue-onigiri.svg" />

---
layout: intro
---

# vue-onigiri

<img v-drag="[309,312,383,216]"  src="/assets/onigiri.jpg" />

<img v-drag="[353,-1,276,276]" src="/assets/vue-onigiri.svg" />

---

# What does it do ?

<v-clicks>

- Serialize VNodes
- Allow one component to know where to retrieve itself
- that's... all

</v-clicks>

<img v-click v-drag="[283,195,448,284]" src="/assets/itaintmuch.png" />

---

<TwoCols gap-2 items-center>

````md magic-move{maxHeight:'40vh'}

```html
<div class="hello-world">
  Hello PragVue !
</div>
```
```html
<div>
  <HelloText name="PragVue">
      
  </HelloText>
</div>
```
```html
<div>
  <HelloText v-load-client name="PragVue">
      
  </HelloText>
</div>
```

```html
<div>
  <HelloText v-load-client name="PragVue">
      <div>
        Even this works !
      </div>
  </HelloText>
</div>
```

````

<div id="second-code-block" class="overflow-auto max-h-[50vh]">

````md magic-move{at:'2', }

```ts
const enum VServerComponentType {
  Element, // 0
  Component, // 1
  Text, // 2
  Fragment, // 3
  Suspense, // 4
}

const ast = [
  VServerComponentType.Element,
  "div",
  {
    class: "hello-world"
  },
  [
    [
      VServerComponentType.Text,
      "Hello PragVue !"
    ]
  ]
]
```

```ts
const enum VServerComponentType {
  Element, // 0
  Component, // 1
  Text, // 2
  Fragment, // 3
  Suspense, // 4
}

const ast = [
  VServerComponentType.Element,
  "div",
  null,
  [
    [
      VServerComponentType.Component,
      {
        name: 'PragVue'
      },
      "/src/HelloText.vue",
      "default",
      {}
    ]
  ]
]
```

```ts
const enum VServerComponentType {
  Element, // 0
  Component, // 1
  Text, // 2
  Fragment, // 3
  Suspense, // 4
}

const ast = [
  VServerComponentType.Element,
  "div",
  null,
  [
    [
      VServerComponentType.Component,
      {
        name: 'PragVue'
      },
      "/src/HelloText.vue",
      "default",
      {
        default: [
          [
            VServerComponentType.Text,
            "Even this works !"
          ]
        ]
      }
    ]
  ]
]
```

````
</div>
</TwoCols>

---

# Vite plugin to provide information about the components location

```ts
import Counter from "../components/Counter.vue"

// build-time
console.log(Counter.__chunk, Counter.__export)
// log '/hash.js', 'default'
```

--- 

# Runtime utilities to serialize and deserialize vnodes
 
<v-switch>

<template #0>

`serializeApp(app: App, context: SSRContext = {}): VServerComponent`

</template>
<template #1>

`serializeComponent(component: Component, props?: any,context: SSRContext = {})`

</template>
<template #2>

`function serializeVNode(vnode: VNodeChild, parentInstance?: ComponentInternalInstance)`

</template>
<template #3>

`function renderOnigiri(input?: VServerComponent,importFn = defaultImportFn,): VNode | undefined`

</template>

</v-switch> 

````md magic-move{at:'0'}

```ts
import "./assets/main.css";
import { serializeApp } from "vue-onigiri/runtime/serialize"
import { createApp, Suspense, h } from "vue";
import App from "./App.vue";

const app = createApp({
    setup() {
        return () =>  h(Suspense, null, {

            default: () => h(App)
        }
        )
    }
}).mount("#app");

serializeApp(app).then(ast => { /* */})
```

```ts
import Counter from "./Counter.vue"
import { serializeComponent } from "vue-onigiri/runtime/serialize"

export default async function (req, res, next) {
  return res.send(JSON.stringify(
    await serializeComponent(Counter, {
      multiplyBy: parseInt(req.query.multiplier) ?? 1
    })
  ))
}
```

```html
<template>
  <Counter @vue:mounted="onVnodeUpdated" @vue:updated="onVnodeUpdated" />
</template>
<script setup lang="ts">
  
async function onVnodeUpdated(vnode: VNode) {
  const ast = await serializeVNode(vnode)
}
</script>
```

```ts
import { renderOnigiri } from "vue-onigiri/runtime/deserialize"

export default defineComponent({
  props: ['ast'],
  setup (props) {
    return () => renderOnigiri(ast)
  }
})
```

```` 




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

<div class="h-[50vh] overflow-auto">
 
```ts
(_ctx: any, _cache: any) => {
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
```

</div>

---

```ts
() => {
  return renderOnigiri(ast.value)
}
```


--- 

# Current state
 
<img  id="building" src="/assets/building.png" class="mx-auto" /> 

---
layout: intro
---

# Roadmap

---
layout: intro
---

<h1>

Preparing for nuxt 
<v-switch>
<template #0>5</template>
<template #1>6 ?</template>
</v-switch>

</h1>
---
layout: intro
---

# Make the plugin compatible with all bundlers with unplugin

---
layout: intro
---

# Build-time render function that returns AST

<div class="text-left">

```ts
export default defineComponent({
  setup() {
    // ...
    if(inject(onigiriSymbol)) {
      return () => {/* ... */}
    }
    
    return () => h(/* ... */)
  },
  renderOnigiri(ctx, slots) {
    return [
      VServerComponentType.Element,
      "div",
      null,
      [
        /* ... */
      ]
    ]
  }
})
```

</div>

---
layout: intro
---

# Thank you ! ❤️


<div>
<logos-github-icon /> @huang-julien
</div>
<div>
<logos-bluesky /> julienhuang-dev.bsky.social
</div>
<div>
<logos-linkedin-icon /> julien-huang
</div>

