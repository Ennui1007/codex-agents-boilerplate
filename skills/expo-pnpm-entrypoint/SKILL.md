---
name: expo-pnpm-entrypoint
description: Fix Expo Go and Metro entrypoint failures in pnpm workspaces. Use when an Expo app shows "main has not been registered", cannot resolve "../../App" from node_modules/expo/AppEntry.js, bundles from a .pnpm path, or after changing Expo SDK/package versions in a pnpm monorepo.
---

---

# Expo pnpm Entrypoint

Use this skill to diagnose Expo Go startup failures caused by pnpm workspace module layout and Expo entrypoint resolution.

## Principle

Treat `"main has not been registered"` as a symptom, not the root cause.
Before changing files, inspect the first Metro or device error above it.

Common root causes:

- Expo entrypoint resolution through a pnpm `.pnpm/.../node_modules/expo/AppEntry.js` real path
- `node_modules/expo/AppEntry.js` importing `../../App` from the wrong directory
- The app being started from the wrong working directory
- A module throwing before `AppRegistry` / `registerRootComponent` runs
- Metro cache retaining an old entrypoint

## Core Fix

In pnpm workspaces, avoid setting the mobile package `main` to `node_modules/expo/AppEntry.js` when Metro resolves it through a `.pnpm/.../node_modules/expo/AppEntry.js` real path. Expo's AppEntry imports `../../App`, which can resolve relative to the pnpm store path instead of the app directory.

Prefer a local entrypoint:

```js
// index.js
import { registerRootComponent } from "expo";

import App from "./App";

registerRootComponent(App);
```

Then set the app package:

```json
{
  "main": "index.js"
}
```

Keep `App.tsx` as the default-exported root component.

Use `index.js` for maximum Expo/Metro compatibility. If the existing project already has a working `index.ts`, keep it and set `main` to `index.ts`.

## Workflow

1. Confirm the app is run from the Expo app package directory, not the monorepo root, unless the repo scripts intentionally start the mobile workspace.
2. Inspect the first Metro error before `"main has not been registered"`.
3. Inspect the app package `main` field and the bundle error import stack.
4. If the stack includes `node_modules/.pnpm/.../node_modules/expo/AppEntry.js` and `Unable to resolve "../../App"`, add the local entrypoint fix above.
5. Add or update a regression test that asserts the mobile package `main` is the local entrypoint.
6. If module resolution still fails, inspect `metro.config.js` for monorepo workspace resolution. Do not rewrite Metro config unless the error clearly points to workspace package resolution.
7. Do not change SDK versions, auth, API, DB schema, notification text, or app UI unless the user explicitly requested that separate work.
8. Clear Metro cache after changing the entrypoint.

## Verification

Run the repo's normal checks:

```sh
pnpm --filter <mobile-package> test
pnpm --filter <mobile-package> typecheck
pnpm --filter <mobile-package> build
cd <mobile-app-dir> && npx expo-doctor
```

Restart Metro from the mobile app directory:

```sh
cd <mobile-app-dir>
pnpm start -- --clear
```

Then reload Expo Go.

Optional deeper bundle check:

```sh
cd <mobile-app-dir>
npx expo export -p ios --clear
```

If `expo export` creates a `dist/` directory, remove it unless the repo intentionally tracks export output.

After verification, check for accidental generated files:

```sh
git status --short
```

## Notes

- `"main has not been registered"` can happen when a module throws before registration. Do not blindly change the entrypoint unless the root error points to AppEntry or entrypoint resolution.
- In managed Expo/CNG projects without `ios/` or `android/`, do not run `expo prebuild` just to fix this issue.
- Prefer the smallest possible change: local entrypoint plus `package.json` `main` update.
