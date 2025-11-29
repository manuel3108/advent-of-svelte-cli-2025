# Advent of Svelte 2025 - Day 2: Svelte CLI Presentation

A presentation app showcasing the **Svelte CLI (`sv`)** for [Advent of Svelte 2025](https://advent.sveltesociety.dev/2025) - Day 2.

## What is this?

This is an interactive presentation built with Svelte and [Animotion](https://animotion.dev) that demonstrates the key features of the Svelte CLI:

-   **`sv create`** - Scaffold new Svelte/SvelteKit projects
-   **`sv add`** - Add integrations like TailwindCSS, Prettier, ESLint, Drizzle, etc.
-   **`sv migrate`** - Upgrade existing projects to Svelte 5
-   **`--from-playground`** - Convert Svelte Playground projects to local development

The presentation is designed for a 9:16 vertical format (ideal for social media/shorts) with animated terminal demonstrations and keypress-triggered animations.

## ⚠️ Disclaimer

**This code was heavily AI-generated due to time constraints.** It should not be considered a reference for best practices, clean architecture, or production-ready code. Use at your own risk and don't judge too harshly! 😅

## Developing

Install dependencies and start the development server:

```sh
pnpm install
pnpm run dev
```

## Controls

-   **Arrow keys** - Navigate between slides
-   **Numpad 0** - Trigger animations (terminal typing, sticky notes, etc.)

## Building

To create a production version:

```sh
pnpm run build
```

You can preview the production build with `pnpm run preview`.
