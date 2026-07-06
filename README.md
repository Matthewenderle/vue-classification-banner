# VueClassificationBanner

A reusable Vue 3 classification banner component for displaying application classification markings such as `UNCLASSIFIED`, `CUI`, `SECRET`, `TOP SECRET`, or `TS//SCI/TK/NOFORN`.

`VueClassificationBanner` is designed to be installed once and reused across multiple Vue and Nuxt projects, so banner behavior, styling, and default classification colors can be updated in one package.

## Examples

![UNCLASSIFIED Banner](./example-unclassified.png)
![CUI Banner](./example-cui.png)
![SECRET Banner](./example-secret.png)
![TOP SECRET Banner](./example-top-secret.png)
![Dynamic TOP SECRET Banner](./example-dynamic-ts.png)

## Features

- Vue 3 component
- TypeScript support
- Global registration through a Vue plugin
- Nuxt module support
- Project-wide defaults
- Per-component prop overrides
- Built-in classification color mapping
- Custom classification text, pretext, banner color, and text color
- Dynamic page content mode
- Scalable banner with proportional sizing

## Installation

```bash
npm install @matthewenderle/vue-classification-banner
```

```bash
pnpm add @matthewenderle/vue-classification-banner
```

```bash
yarn add @matthewenderle/vue-classification-banner
```

## Vue Usage

Register the plugin in `main.ts`.

```ts
import { createApp } from "vue";
import App from "./App.vue";

import VueClassificationBanner from "@matthewenderle/vue-classification-banner";
import "@matthewenderle/vue-classification-banner/style.css";

createApp(App)
  .use(VueClassificationBanner, {
    text: import.meta.env.VITE_CLASSIFICATION,
  })
  .mount("#app");
```

Then place the banner near the root of your app.

```vue
<!-- App.vue -->
<template>
  <ClassificationBanner />
  <RouterView />
</template>
```

## Vite Environment Variable Example

For Vite-based Vue apps, use a `VITE_`-prefixed variable so the value is available in client-side code.

```env
VITE_CLASSIFICATION=TS//SCI/TK/NOFORN
```

Then pass it into the plugin.

```ts
app.use(VueClassificationBanner, {
  text: import.meta.env.VITE_CLASSIFICATION,
});
```

Do not put secrets in client-exposed environment variables. The classification banner text is rendered in the browser and is visible to users.

## Props

The banner can be configured globally through the plugin or locally through props.

```vue
<ClassificationBanner text="CUI" color="#502B85" text-color="#fff" />
```

| Prop             | Type      | Default        | Description                                                                                                                                           |
| ---------------- | --------- | -------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------- |
| `text`           | `string`  | `UNCLASSIFIED` | Classification text shown in the banner.                                                                                                              |
| `pretext`        | `string`  | ``             | Text shown before the classification.                                                                                                                 |
| `color`          | `string`  | Auto-resolved  | Background and top-border color for the banner.                                                                                                       |
| `textColor`      | `string`  | Auto-resolved  | Text color used inside the banner. In templates, use `text-color`.                                                                                    |
| `scale`          | `number`  | `1`            | Scale factor for all banner dimensions. `1.2` makes everything 20% larger, `0.8` makes it 20% smaller.                                                |
| `dynamicContent` | `boolean` | `false`        | When `true`, displays "DYNAMIC PAGE - HIGHEST POSSIBLE CLASSIFICATION IS [text]" instead of the default pretext. In templates, use `dynamic-content`. |

Props override plugin-level defaults.

### Scale Example

Scale the banner proportionally:

```vue
<ClassificationBanner :scale="1.5" />
```

Or set it globally:

```ts
app.use(VueClassificationBanner, {
  text: "CUI",
  scale: 1.2,
});
```

### Dynamic Content Example

For dynamic pages, use `dynamicContent` to change the banner text:

```vue
<ClassificationBanner :dynamic-content="true" text="CUI" />
<!-- Shows: "DYNAMIC PAGE - HIGHEST POSSIBLE CLASSIFICATION IS CUI" -->
```

Default behavior (dynamicContent: false):

```vue
<ClassificationBanner text="CUI" />
<!-- Shows: "CUI" -->
```

## Default Color Mapping

The package includes default colors for common classification markings.

| Classification | Default Color |
| -------------- | ------------- |
| `CUI`          | `#502B85`     |
| `UNCLASSIFIED` | `#007A33`     |
| `CONFIDENTIAL` | `#0033A0`     |
| `SECRET`       | `#C8102E`     |
| `TOP SECRET`   | `#FF8C00`     |
| `TS//SCI`      | `#FCE83A`     |

Extended markings such as `TS//SCI/TK/NOFORN` automatically use the `TS//SCI` color unless a custom color is provided.

```vue
<ClassificationBanner text="TS//SCI/TK/NOFORN" />
```

Override the default color when needed.

```vue
<ClassificationBanner
  text="TS//SCI/TK/NOFORN"
  color="#FCE83A"
  text-color="#000"
/>
```

## Global Defaults

Configure defaults once when installing the plugin.

```ts
app.use(VueClassificationBanner, {
  text: "CUI",
  color: "#502B85",
  textColor: "#fff",
  scale: 1.1,
  dynamicContent: true,
});
```

Then render the component without props.

```vue
<ClassificationBanner />
```

## CSS Variables

The banner exposes a global CSS variable for use in your app:

- `--classification-banner-height`: The calculated height of the banner including the top stripe. Updates when the `scale` prop changes.

Use this to offset your app content:

```vue
<!-- App.vue -->
<template>
  <ClassificationBanner :scale="1.2" />
  <div class="app-container">
    <!-- your app content -->
  </div>
</template>

<style>
.app-container {
  padding-top: var(--classification-banner-height);
}
</style>
```

## Local Overrides

Local props take precedence over global defaults.

```vue
<template>
  <ClassificationBanner text="SECRET" />
</template>
```

If the global default is `CUI`, this instance will still render `SECRET`.

## Direct Component Import

If you do not want global registration, import the component directly.

```vue
<script setup lang="ts">
import { ClassificationBanner } from "vue-classification-banner";
import "vue-classification-banner/style.css";
</script>

<template>
  <ClassificationBanner text="UNCLASSIFIED" />
</template>
```

## Nuxt Usage

Add the Nuxt module to `nuxt.config.ts`.

```ts
export default defineNuxtConfig({
  modules: ["vue-classification-banner/nuxt"],

  classificationBanner: {
    text: "UNCLASSIFIED",
    scale: 1.1,
    dynamicContent: false,
  },
});
```

Then place the banner near the root of your Nuxt app.

```vue
<!-- app.vue -->
<template>
  <ClassificationBanner />
  <NuxtPage />
</template>
```

## Nuxt Environment Variable Example

Use Nuxt public runtime config for client-visible values.

```ts
export default defineNuxtConfig({
  modules: ["vue-classification-banner/nuxt"],

  runtimeConfig: {
    public: {
      classificationBanner: {
        text: "",
        scale: 1,
        dynamicContent: false,
      },
    },
  },
});
```

Or use the `CLASSIFICATION_SCALE` environment variable:

```bash
CLASSIFICATION_SCALE=1.2 npm run dev
```

Then define the values in `.env`.

```env
NUXT_PUBLIC_CLASSIFICATION_BANNER_TEXT=TS//SCI/TK/NOFORN
CLASSIFICATION_SCALE=1.2
```

The `CLASSIFICATION_SCALE` environment variable is read at build time.

Because banner values are displayed in the browser, they should not be treated as secret.

## TypeScript

The package exports useful types.

```ts
import type { ClassificationBannerOptions } from "vue-classification-banner";

const options: ClassificationBannerOptions = {
  text: "CUI",
  color: "#502B85",
};
```

## Package Development

Install dependencies.

```bash
npm install
```

Type-check the project.

```bash
npm run typecheck
```

Build the Nuxt module and Vue library output.

```bash
npm run build
```

The build outputs files into `dist/`.

## Publishing

Build and publish to npm.

```bash
npm run build
npm publish
```

For a private registry or GitHub Packages, update `name`, `repository`, and `publishConfig` in `package.json`.

## Notes

The default colors are provided as convenience mappings and can be overridden with props or plugin options. Confirm your project's marking, display, and compliance requirements before using this package in production environments.

## License

MIT
