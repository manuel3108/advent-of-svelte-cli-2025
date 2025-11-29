<script lang="ts">
    import { Presentation, Slide } from '@animotion/core';
    import Snowfall from '$lib/components/Snowfall.svelte';
    import Terminal from '$lib/components/Terminal.svelte';
    import StickyNotes from '$lib/components/StickyNotes.svelte';
    import FileTree from '$lib/components/FileTree.svelte';
    import PlaygroundScreenshot from '$lib/components/PlaygroundScreenshot.svelte';
</script>

<div class="presentation-container">
    <div class="frame-9-16">
        <Presentation>
            <!-- Intro Slide (0:00–0:03) -->
            <Slide class="slide">
                <Snowfall />
                <div class="code-editor-bg intro-bg"></div>
                <div class="content">
                    <svg
                        class="svelte-logo-large"
                        viewBox="0 0 98.1 118"
                        width="80"
                        height="96"
                    >
                        <path
                            fill="#ff3e00"
                            d="M91.8 15.6C80.9-.1 59.2-4.7 43.6 5.2L16.1 22.8C8.6 27.5 3.4 35.2 1.9 43.9c-1.3 7.3-.2 14.8 3.3 21.3-2.4 3.6-4 7.6-4.7 11.8-1.6 8.9.5 18.1 5.7 25.4 11 15.7 32.6 20.3 48.2 10.4l27.5-17.5c7.5-4.7 12.7-12.4 14.2-21.1 1.3-7.3.2-14.8-3.3-21.3 2.4-3.6 4-7.6 4.7-11.8 1.7-9-.4-18.2-5.7-25.5"
                        />
                        <path
                            fill="#fff"
                            d="M40.9 103.9c-8.9 2.3-18.2-1.2-23.4-8.7-3.2-4.4-4.4-9.9-3.5-15.3.2-.9.4-1.7.6-2.6l.5-1.6 1.4 1c3.3 2.4 6.9 4.2 10.8 5.4l1 .3-.1 1c-.1 1.4.3 2.9 1.1 4.1 1.6 2.3 4.4 3.4 7.1 2.7.6-.2 1.2-.4 1.7-.7l27.4-17.4c1.4-.9 2.3-2.2 2.6-3.8.3-1.6-.1-3.3-1-4.6-1.6-2.3-4.4-3.3-7.1-2.6-.6.2-1.2.4-1.7.7l-10.5 6.7c-1.7 1.1-3.6 1.9-5.6 2.4-8.9 2.3-18.2-1.2-23.4-8.7-3.1-4.4-4.4-9.9-3.4-15.3.9-5.2 4.1-9.9 8.6-12.7l27.5-17.5c1.7-1.1 3.6-1.9 5.6-2.5 8.9-2.3 18.2 1.2 23.4 8.7 3.2 4.4 4.4 9.9 3.5 15.3-.2.9-.4 1.7-.7 2.6l-.5 1.6-1.4-1c-3.3-2.4-6.9-4.2-10.8-5.4l-1-.3.1-1c.1-1.4-.3-2.9-1.1-4.1-1.6-2.3-4.4-3.3-7.1-2.6-.6.2-1.2.4-1.7.7L32.4 46.1c-1.4.9-2.3 2.2-2.6 3.8s.1 3.3 1 4.6c1.6 2.3 4.4 3.3 7.1 2.6.6-.2 1.2-.4 1.7-.7l10.5-6.7c1.7-1.1 3.6-1.9 5.6-2.5 8.9-2.3 18.2 1.2 23.4 8.7 3.2 4.4 4.4 9.9 3.5 15.3-.9 5.2-4.1 9.9-8.6 12.7l-27.5 17.5c-1.7 1.1-3.6 1.9-5.6 2.4"
                        />
                    </svg>
                    <h1 class="title">
                        <span class="day">Day 2</span>
                        <span class="main-title">Svelte CLI in 2025</span>
                    </h1>
                </div>
            </Slide>

            <!-- sv create (0:20–0:40) -->
            <Slide class="slide">
                <Snowfall density={10} />
                <div class="content">
                    <h2 class="command-title">
                        <span class="prefix">sv</span> create
                    </h2>
                    <Terminal command="npx sv create my-app" typing={true} />
                    <StickyNotes flags={['--template', '--types', '--add']} />
                </div>
            </Slide>

            <!-- sv add (0:40–1:00) -->
            <Slide class="slide">
                <Snowfall density={10} />
                <div class="content">
                    <h2 class="command-title">
                        <span class="prefix">sv</span> add
                    </h2>
                    <Terminal command="npx sv add tailwindcss" typing={true} />
                    <StickyNotes
                        flags={[
                            'tailwindcss',
                            'playwright',
                            'storybook',
                            'lucia',
                        ]}
                        variant="packages"
                    />
                </div>
            </Slide>

            <!-- sv migrate (1:00–1:20) -->
            <Slide class="slide">
                <Snowfall density={10} />
                <div class="content">
                    <h2 class="command-title">
                        <span class="prefix">sv</span> migrate
                    </h2>
                    <FileTree />
                    <Terminal command="npx sv migrate svelte-5" typing={true} />
                </div>
            </Slide>

            <!-- --from-playground (1:20–1:55) -->
            <Slide class="slide">
                <Snowfall density={10} />
                <div class="content">
                    <h2 class="command-title">
                        <span class="prefix">--</span>from-playground
                    </h2>
                    <PlaygroundScreenshot />
                    <Terminal
                        pasteUrl="svelte.dev/playground/abc123def"
                        commandPrefix={'npx sv create --from-playground="'}
                        commandSuffix={'"'}
                        pasteDelay={2200}
                    />
                </div>
            </Slide>

            <!-- Outro (1:55–2:00) -->
            <Slide class="slide">
                <Snowfall density={20} />
                <div class="content">
                    <div class="recap-window">
                        <div class="recap-window-header">
                            <div class="window-buttons">
                                <span class="btn close"></span>
                                <span class="btn minimize"></span>
                                <span class="btn maximize"></span>
                            </div>
                            <span class="window-title">sv commands</span>
                        </div>
                        <div class="recap-window-body">
                            <div class="recap-commands">
                                <div class="recap-item">
                                    <span class="recap-prefix">$</span>
                                    <span class="recap-cmd">sv create</span>
                                </div>
                                <div class="recap-item">
                                    <span class="recap-prefix">$</span>
                                    <span class="recap-cmd">sv add</span>
                                </div>
                                <div class="recap-item">
                                    <span class="recap-prefix">$</span>
                                    <span class="recap-cmd">sv migrate</span>
                                </div>
                                <div class="recap-item">
                                    <span class="recap-prefix">$</span>
                                    <span class="recap-cmd"
                                        >sv create --from-playground</span
                                    >
                                </div>
                            </div>
                            <div class="recap-cta">
                                <span class="cta-arrow">→</span>
                                <code class="cta-command">npx sv</code>
                            </div>
                        </div>
                    </div>
                </div>
            </Slide>
        </Presentation>
    </div>
</div>

<style>
    :global(body) {
        margin: 0;
        padding: 0;
        background: #0a0a14;
        overflow: hidden;
    }

    .presentation-container {
        width: 100vw;
        height: 100vh;
        display: flex;
        justify-content: center;
        align-items: center;
        background: #0a0a14;
    }

    /* 9:16 frame container */
    .frame-9-16 {
        width: calc(100vh * 9 / 16);
        height: 100vh;
        max-width: 100vw;
        max-height: calc(100vw * 16 / 9);
        position: relative;
        overflow: hidden;
    }

    :global(.animotion) {
        width: 100% !important;
        height: 100% !important;
    }

    :global(.slide) {
        display: flex !important;
        flex-direction: column;
        justify-content: center;
        align-items: center;
        padding: 3rem 1.5rem;
        position: relative;
        overflow: hidden;
        background: linear-gradient(
            180deg,
            #1a1a2e 0%,
            #16213e 40%,
            #0f3460 100%
        ) !important;
        width: 100% !important;
        height: 100% !important;
    }

    .content {
        position: relative;
        z-index: 10;
        display: flex;
        flex-direction: column;
        align-items: center;
        gap: 1.5rem;
        text-align: center;
        width: 96%;
        padding: 0;
    }

    /* Intro Slide Styles */
    .code-editor-bg.intro-bg {
        position: absolute;
        inset: 0;
        background: repeating-linear-gradient(
            0deg,
            transparent,
            transparent 1.5rem,
            rgba(255, 255, 255, 0.02) 1.5rem,
            rgba(255, 255, 255, 0.02) 1.6rem
        );
        opacity: 0.5;
    }

    .title {
        display: flex;
        flex-direction: column;
        gap: 0.75rem;
    }

    .day {
        font-size: 1.4rem;
        font-weight: 600;
        color: #ff3e00;
        text-transform: uppercase;
        letter-spacing: 0.2em;
        text-shadow: 0 0 20px rgba(255, 62, 0, 0.5);
    }

    .main-title {
        font-size: 2rem;
        font-weight: 800;
        background: linear-gradient(
            135deg,
            #ff3e00 0%,
            #ff6b35 50%,
            #f7931a 100%
        );
        -webkit-background-clip: text;
        -webkit-text-fill-color: transparent;
        background-clip: text;
        line-height: 1.2;
    }

    .subtitle {
        font-size: 1rem;
        color: #a8b2d1;
        margin-top: 1rem;
    }

    .svelte-logo-large {
        margin-bottom: 1rem;
        filter: drop-shadow(0 0 30px rgba(255, 62, 0, 0.4));
    }

    /* Command Titles */
    .command-title {
        font-size: 1.8rem;
        font-weight: 800;
        font-family: 'JetBrains Mono', 'Fira Code', monospace;
        color: #ccd6f6;
    }

    .command-title .prefix {
        color: #64ffda;
    }

    /* Outro Styles */
    .outro-title {
        font-size: 1.6rem;
        font-weight: 700;
        color: #ccd6f6;
        margin-top: 1.5rem;
    }

    .next-topic {
        font-size: 1rem;
        color: #64ffda;
        margin-top: 0.5rem;
    }

    /* Recap Window */
    .recap-window {
        width: 100%;
        background: #0d1117;
        border-radius: 12px;
        overflow: hidden;
        border: 1px solid #30363d;
        font-family: 'JetBrains Mono', monospace;
        box-shadow: 0 8px 32px rgba(0, 0, 0, 0.4);
    }

    .recap-window-header {
        display: flex;
        align-items: center;
        padding: 0.6rem 0.9rem;
        background: #161b22;
        border-bottom: 1px solid #30363d;
        position: relative;
    }

    .window-buttons {
        display: flex;
        gap: 0.4rem;
    }

    .recap-window-header .btn {
        width: 12px;
        height: 12px;
        border-radius: 50%;
    }

    .recap-window-header .btn.close {
        background: #ff5f56;
    }

    .recap-window-header .btn.minimize {
        background: #ffbd2e;
    }

    .recap-window-header .btn.maximize {
        background: #27ca40;
    }

    .window-title {
        position: absolute;
        left: 50%;
        transform: translateX(-50%);
        font-size: 0.75rem;
        color: #8b949e;
        font-weight: 500;
    }

    .recap-window-body {
        padding: 1.2rem;
    }

    .recap-commands {
        display: flex;
        flex-direction: column;
        gap: 0.6rem;
        margin-bottom: 1rem;
    }

    .recap-item {
        display: flex;
        align-items: center;
        gap: 0.5rem;
        font-size: 1rem;
    }

    .recap-prefix {
        color: #64ffda;
        font-weight: 600;
    }

    .recap-cmd {
        color: #e6edf3;
    }

    .recap-cta {
        display: flex;
        align-items: center;
        gap: 0.75rem;
        padding-top: 1rem;
        border-top: 1px solid #30363d;
    }

    .cta-arrow {
        color: #64ffda;
        font-size: 1.2rem;
    }

    .cta-command {
        font-family: 'JetBrains Mono', monospace;
        font-size: 1.1rem;
        font-weight: 600;
        color: #64ffda;
        background: rgba(100, 255, 218, 0.1);
        padding: 0.5rem 1rem;
        border-radius: 6px;
        border: 1px solid rgba(100, 255, 218, 0.3);
    }

    /* Hide Animotion UI elements */
    :global(.progress) {
        display: none !important;
    }

    :global(.controls) {
        display: none !important;
    }

    :global([class*='slide-number']) {
        display: none !important;
    }

    :global(button) {
        display: none !important;
    }
</style>
