---
layout: post
title: "Upgrade wordle helper to vite"
date: 2024-09-24 11:07:55 +0000
categories: personal
toc: false
---
# The app
I created an application to help me solve wordl puzzles [wordle helper](https://wh.johncooke.info). I originally created it using create react app, which is unfortunately now not supported. There are several security issues flagged by dependabot so I needed to update to something. The source is on [github](https://github.com/Cookiesworld/wordle-solver/commit/6bed2b83e0927815ef6fd1d833e2e090c75038b6#diff-a32a0887ed9d1d707bbb3b845b7df7fd40e673c47e7b60a3ebd896b68d3b8839R36)

I decided to switch to [vite](https://vitejs.dev/guide/) on the basis that is offeres a modern fast startup and developer experience. 

To start with I ran the 
```
npm create vite@latest . -- --template react
```

I chose to ignore files and continue, thie replaced my exsting package.conf. I had to rejig some of the structure, index.html moves from the public folder to the root. 

VITE doesnt directly expose process.env and only imports environment variables which are prefixed with VITE. A quick find and replace later.

I am really impressed how fast VITE starts up.

# The tests

Getting the unit tests running was a little more tricky.

First I installed vitest
```
npm install -D vitest 
```
Next modified the vite.config.js to add a test section to vite.config.js

```
/// <reference types="vitest/config" />
import { defineConfig } from 'vite';
import react from '@vitejs/plugin-react';
// https://vitejs.dev/config/
export default defineConfig({
  plugins: [react()],
  test: {
    environment: 'jsdom',
    setupFiles: [
      './test/setupTests.js',
    ]
  }
});
```

This references a new setupTests.js file
``` 
import * as matchers from "@testing-library/jest-dom/matchers";
expect.extend(matchers);
afterEach(() => {
  cleanup();
});
```
Vitest doesnt use globlas like jest so define, it, expect needed to be referenced in the tests.

# Linting

Finally I added linting to the project, startng by installing eslint 
```
npm install -D eslint vite-plugin-eslint 
```
Then added the eslint to the vite.config.js
```
/// <reference types="vitest/config" />
import { defineConfig } from 'vite';
import react from '@vitejs/plugin-react';
import eslint from 'vite-plugin-eslint';
// https://vitejs.dev/config/
export default defineConfig({
  plugins: [react(), eslint()],
  server: {
    port: 3022,
  },
  test: {
    environment: 'jsdom',
    setupFiles: [
      './test/setupTests.js',
    ],
    coverage: {
      provider: 'v8',
      reporter: ['text', 'html'],
    },
  }
});
```
Finally tweaked the ruleset in eslint.config.js. I didnt want to add proptypes yet so I made that a warning by adding this to the rules section

```
 'react/prop-types': 'warn'
```
