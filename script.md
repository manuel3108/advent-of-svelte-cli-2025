### Intro

Hey everyone, welcome back to Advent of Svelte! Today we're diving into the Svelte CLI - a tool you'll want to know about.

### sv create

Let's start with `sv create`. Run the command with your project name, and you'll be guided through an interactive setup to scaffold a new SvelteKit project.

And if you want to automate things, there are flags like `--template`, `--types`, or `--add` to skip the prompts entirely.

### sv add

Next up: `sv add`. This lets you add add-ons to any existing project. Things like Tailwind for styling, Drizzle for databases, vitest for testing - even an MCP server. And yes, you can run it non-interactively too.

### sv migrate

Still on Svelte 4 or even older? Then run `sv migrate svelte-5`. This will transform your code by updating your components to use runes. It won't catch everything, but it'll get you most of the way there.

### --from-playground

What I've shown so far has been around for a while. But here's something new - and you'll probably use this one a lot during this years Advent of Svelte.

We've all been there: you prototype in the playground and want to continue locally. This usually ment: download the zip file, extract it, install dependencies, and make sure you didnt break anything.

Now? Just copy the playground URL, head to your terminal, and run `sv create --from-playground`. Paste the URL, and you're done. All your files are there, and it even asks if you want to add add-ons like Tailwind or Drizzle on top. One command and you are fully set up.

### Outro

That's the Svelte CLI. Give it a try using `npx sv` - and see you in the cli!

# Intro

Hey everyone! Welcome back to Advent of Svelte. Today we’re taking a look at the Svelte CLI.

# sv create

We’ll start with a command you’ll use pretty often: `sv create`.
Run it with a project name and it walks you through a quick setup to generate a new SvelteKit project.

If you already know what you want, you can skip the prompts. Use flags like `--template`, `--types`, or `--add` to preconfigure things automatically.

# sv add

Then there’s `sv add`. This is how you bring new functionality into an existing project. Need Tailwind, Drizzle, Vitest or an MCP server? Just add them with one single command.

# sv migrate

If you’re still on Svelte 4 or earlier, `sv migrate svelte-5` is your friend. It updates your components to use runes and handles a lot of the heavy lifting for you. You may need to do a few manual tweaks afterwards, but it saves a ton of time compared to doing everything yourself.

# --from-playground

Everything so far has been part of the CLI for a bit, but this next feature is quite new — and it’s going to helpful throughout this year’s Advent of Svelte.

If you’ve ever built something in the playground and wanted to continue locally, you probably know the drill: download the ZIP file, unpack it, install dependencies, and make sure you didn't break anything while doing so.

Now?
Just copy the playground URL and run `sv create --from-playground`. Paste the link, press enter, and the CLI generates a complete SvelteKit project from your playground. All the files are there, dependencies are installed, and it even asks whether you want to add extras like Tailwind or Drizzle.

# Outro

And that’s the Svelte CLI in a nutshell. Try it out with npx sv
