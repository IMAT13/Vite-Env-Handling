# Handling Environment Configurations in Vue Projects

This document describes the steps to handling of different environments in a Vue project using Vite.

## Step 1: Update Vite Configuration

1. Open `vite.config.js`.
2. Update the existing content with the following modifications:

```javascript
import { fileURLToPath, URL } from "node:url";
import { defineConfig, loadEnv } from "vite";
import vue from "@vitejs/plugin-vue";
import vueJsx from "@vitejs/plugin-vue-jsx";

const ENV_DIR = "./env";

export default ({ mode = "dev" } = {}) => {
  process.env = { ...process.env, ...loadEnv(mode, ENV_DIR, "") };

  const isDevelopmentMode = process.env.NODE_ENV === "development";
  const PUBLIC_PATH = isDevelopmentMode ? "/" : "/public/";

  return defineConfig({
    base: PUBLIC_PATH,
    envDir: ENV_DIR,
    plugins: [vue(), vueJsx()],
    resolve: {
      alias: {
        "@": fileURLToPath(new URL("./src", import.meta.url)),
      },
    },
    css: {
      preprocessorOptions: {
        scss: {
          additionalData: \`@import "@/assets/style/main";\`,
        },
      },
    },
    optimizeDeps: {
      exclude: ["js-big-decimal"],
    },
  });
};
```

## Step 2: Update Package Configuration

1. Open `package.json`.
2. update the existing scripts section based on the following:

```json
{
  "scripts": {
    "dev": "vite --mode dev",
    "dev:test": "vite --mode test",
    "dev:prod": "vite --mode prod",
    "build:dev": "vite build --mode dev",
    "build:test": "vite build --mode test",
    "build:prod": "vite build --mode prod",
    "preview": "vite preview",
    "test:unit": "vitest"
  }
}
```

## Step 3: Update Vitest Configuration

1. Open `vitest.config.js`.
2. update the vitest config if necessary:

```javascript
import { fileURLToPath } from "node:url";
import { mergeConfig, defineConfig, configDefaults } from "vitest/config";
import viteConfig from "./vite.config";

export default () =>
  mergeConfig(
    viteConfig(),
    defineConfig({
      test: {
        environment: "jsdom",
        exclude: [...configDefaults.exclude, "e2e/*"],
        root: fileURLToPath(new URL("./", import.meta.url)),
      },
    })
  );
```

## Step 4: Create Environment Variable Files

1. Create a folder named `env` in the root of the project.
2. Inside this folder, create environment-specific files such as `.env.dev`, `.env.test`, and `.env.prod`.
3. Add your environment variables in these files. For example:

**.env.dev**

```
VITE_API_URL=https://dev.api.example.com
VITE_ANOTHER_VAR=devValue
```

**.env.test**

```
VITE_API_URL=https://test.api.example.com
VITE_ANOTHER_VAR=testValue
```

**.env.prod**

```
VITE_API_URL=https://api.example.com
VITE_ANOTHER_VAR=prodValue
```

---

Following these steps will configure your Vue project to handle different environments using Vite.
