<script lang="ts">
    import { onMount } from 'svelte';

    interface Props {
        /** Key to reset state and prepare for new animation */
        slideKey?: number;
        /** Callback when mouse animation completes */
        onMouseAnimationComplete?: () => void;
        /** Callback when state is reset (e.g., when leaving view) */
        onReset?: () => void;
    }

    let { slideKey = 0, onMouseAnimationComplete, onReset }: Props = $props();

    let containerEl: HTMLDivElement;
    let cursorX = $state(70);
    let cursorY = $state(70);
    let urlSelected = $state(false);
    let showCopiedToast = $state(false);
    let cursorVisible = $state(false);
    let isVisible = $state(false);
    let keypressCount = $state(0);
    let previousSlideKey = slideKey;

    // Reset state when slideKey changes
    $effect(() => {
        if (slideKey !== previousSlideKey) {
            previousSlideKey = slideKey;
            resetState();
        }
    });

    function resetState() {
        cursorX = 70;
        cursorY = 70;
        urlSelected = false;
        showCopiedToast = false;
        cursorVisible = false;
        keypressCount = 0;
        onReset?.();
    }

    function runAnimation() {
        // Reset cursor position but make it visible
        cursorX = 70;
        cursorY = 70;
        urlSelected = false;
        showCopiedToast = false;
        cursorVisible = true;

        // Phase 1: Move cursor to URL bar (center of url-bar roughly)
        setTimeout(() => {
            cursorX = 60;
            cursorY = 12;
        }, 400);

        // Phase 2: Select URL (triple click)
        setTimeout(() => {
            urlSelected = true;
        }, 1000);

        // Phase 3: Show copied toast (Ctrl+C)
        setTimeout(() => {
            showCopiedToast = true;
        }, 1400);

        // Phase 4: Hide cursor and toast, deselect URL (cursor goes to terminal)
        setTimeout(() => {
            cursorVisible = false;
            showCopiedToast = false;
            urlSelected = false;
            // Notify parent that mouse animation is complete
            onMouseAnimationComplete?.();
        }, 2000);
    }

    // Handle keypress for triggering animation
    function handleKeypress(event: KeyboardEvent) {
        // Use Enter key as trigger (doesn't conflict with Reveal.js navigation)
        if (event.key === 'Enter' && isVisible) {
            if (keypressCount === 0) {
                event.preventDefault();
                event.stopPropagation();
                // First keypress: start mouse animation
                keypressCount = 1;
                runAnimation();
            }
        }
    }

    // React to visibility changes
    let wasVisible = false;
    let initialized = false;
    $effect(() => {
        if (isVisible && !wasVisible && initialized) {
            // Add keypress listener when becoming visible
            window.addEventListener('keydown', handleKeypress);
        }
        if (!isVisible && wasVisible) {
            // Reset when leaving view
            resetState();
            // Remove keypress listener when leaving view
            window.removeEventListener('keydown', handleKeypress);
        }
        wasVisible = isVisible;
    });

    onMount(() => {
        const observer = new IntersectionObserver(
            (entries) => {
                entries.forEach((entry) => {
                    isVisible =
                        entry.isIntersecting && entry.intersectionRatio >= 0.5;
                });
            },
            { threshold: [0, 0.5, 1] }
        );

        requestAnimationFrame(() => {
            observer.observe(containerEl);
            requestAnimationFrame(() => {
                initialized = true;
            });
        });

        return () => {
            observer.disconnect();
            // Clean up keypress listener if it was added
            window.removeEventListener('keydown', handleKeypress);
        };
    });
</script>

<div class="browser" bind:this={containerEl}>
    <!-- Browser Chrome -->
    <div class="browser-header">
        <div class="window-controls">
            <span class="dot red"></span>
            <span class="dot yellow"></span>
            <span class="dot green"></span>
        </div>
        <div class="url-bar" class:selected={urlSelected}>
            <span class="lock">🔒</span>
            <span class="url">svelte.dev/playground/abc123def</span>
        </div>
    </div>

    <!-- Copied Toast -->
    {#if showCopiedToast}
        <div class="copied-toast">📋 Copied!</div>
    {/if}

    <!-- Browser Content -->
    <div class="browser-content">
        <!-- Playground Header -->
        <div class="playground-header">
            <svg
                class="svelte-logo"
                viewBox="0 0 98.1 118"
                width="16"
                height="20"
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
            <span class="logo-text">SVELTE</span>
            <span class="playground-label">playground</span>
        </div>

        <!-- Files -->
        <div class="section-row">
            <span class="section-label">📁</span>
            <div class="file active">App.svelte</div>
            <div class="file">utils.js</div>
        </div>

        <!-- Code -->
        <div class="code-section">
            <div class="line"><span class="tag">&lt;script&gt;</span></div>
            <div class="line">
                &nbsp;&nbsp;<span class="keyword">let</span>&nbsp;<span
                    class="variable">count</span
                >&nbsp;<span class="operator">=</span>&nbsp;<span class="state"
                    >$state</span
                ><span class="paren">(</span><span class="number">0</span><span
                    class="paren">)</span
                ><span class="punctuation">;</span>
            </div>
            <div class="line"><span class="tag">&lt;/script&gt;</span></div>
            <div class="line">&nbsp;</div>
            <div class="line">
                <span class="tag">&lt;button&gt;</span><span class="text"
                    >Clicks:&nbsp;</span
                ><span class="brace">{'{'}</span><span class="variable"
                    >count</span
                ><span class="brace">{'}'}</span><span class="tag"
                    >&lt;/button&gt;</span
                >
            </div>
        </div>

        <!-- Result -->
        <div class="result-section">
            <span class="result-label">Result:</span>
            <button class="demo-btn">Clicks: 0</button>
        </div>
    </div>

    <!-- Animated Cursor -->
    {#if cursorVisible}
        <svg
            class="cursor-pointer"
            style="left: {cursorX}%; top: {cursorY}%;"
            width="24"
            height="24"
            viewBox="0 0 24 24"
            fill="none"
        >
            <path
                d="M5.5 3.21V20.8c0 .45.54.67.85.35l4.86-4.86a.5.5 0 0 1 .35-.15h6.87c.48 0 .72-.58.38-.92L6.35 2.85a.5.5 0 0 0-.85.36Z"
                fill="#fff"
                stroke="#000"
                stroke-width="1.5"
            />
        </svg>
    {/if}
</div>

<style>
    .browser {
        width: 100%;
        background: #1a1a2e;
        border-radius: 12px;
        overflow: hidden;
        box-shadow:
            0 10px 40px rgba(0, 0, 0, 0.6),
            0 0 0 1px rgba(255, 255, 255, 0.1);
        font-family: 'Inter', sans-serif;
        position: relative;
    }

    .browser-header {
        display: flex;
        align-items: center;
        gap: 0.6rem;
        padding: 0.5rem 0.7rem;
        background: #161b22;
        border-bottom: 1px solid #30363d;
    }

    .window-controls {
        display: flex;
        gap: 0.35rem;
    }

    .dot {
        width: 10px;
        height: 10px;
        border-radius: 50%;
    }

    .dot.red {
        background: #ff5f57;
    }
    .dot.yellow {
        background: #ffbd2e;
    }
    .dot.green {
        background: #28c840;
    }

    .url-bar {
        flex: 1;
        display: flex;
        align-items: center;
        gap: 0.5rem;
        background: #21262d;
        padding: 0.5rem 0.8rem;
        border-radius: 6px;
        border: 1px solid #30363d;
        transition: all 0.3s ease;
    }

    .url-bar.selected {
        background: #1a3a5c;
        box-shadow: 0 0 0 2px #58a6ff;
    }

    .lock {
        font-size: 0.8rem;
    }

    .url {
        font-size: 0.85rem;
        color: #e6edf3;
        font-family: 'Inter', sans-serif;
    }

    .url-bar.selected .url {
        color: #58a6ff;
    }

    .copied-toast {
        position: absolute;
        top: 50px;
        left: 50%;
        transform: translateX(-50%);
        background: #238636;
        color: white;
        padding: 0.5rem 1rem;
        border-radius: 6px;
        font-size: 0.85rem;
        font-weight: 600;
        z-index: 100;
        animation: fadeIn 0.2s ease;
    }

    @keyframes fadeIn {
        from {
            opacity: 0;
            transform: translateX(-50%) translateY(-10px);
        }
        to {
            opacity: 1;
            transform: translateX(-50%) translateY(0);
        }
    }

    .browser-content {
        padding: 0.5rem;
        background: #0d1117;
    }

    .playground-header {
        display: flex;
        align-items: center;
        gap: 0.4rem;
        padding: 0.5rem 0.6rem;
        background: #161b22;
        border-radius: 6px;
        margin-bottom: 0.5rem;
    }

    .svelte-logo {
        width: 18px;
        height: 22px;
    }

    .logo-text {
        font-size: 0.75rem;
        font-weight: 700;
        color: #fff;
        letter-spacing: 0.05em;
    }

    .playground-label {
        font-size: 0.7rem;
        color: #8b949e;
    }

    .section-row {
        display: flex;
        align-items: center;
        gap: 0.4rem;
        padding: 0.4rem 0.5rem;
        background: #161b22;
        border-radius: 4px;
        margin-bottom: 0.4rem;
    }

    .section-label {
        font-size: 0.8rem;
    }

    .file {
        font-size: 0.75rem;
        color: #8b949e;
        padding: 0.2rem 0.5rem;
        border-radius: 3px;
        background: #21262d;
    }

    .file.active {
        background: #ff3e00;
        color: white;
    }

    .code-section {
        font-family: 'JetBrains Mono', monospace;
        font-size: 0.95rem;
        line-height: 1.5;
        padding: 0.7rem;
        background: #0d1117;
        border: 1px solid #30363d;
        border-radius: 4px;
        margin-bottom: 0.4rem;
        text-align: left;
    }

    .line {
        white-space: nowrap;
        text-align: left;
    }
    .tag {
        color: #7ee787;
    }
    .keyword {
        color: #ff7b72;
    }
    .variable {
        color: #79c0ff;
    }
    .state {
        color: #ffa657;
    }
    .number {
        color: #79c0ff;
    }
    .text {
        color: #a5d6ff;
    }
    .brace {
        color: #ffa657;
    }
    .operator {
        color: #e6edf3;
    }
    .paren {
        color: #e6edf3;
    }
    .punctuation {
        color: #e6edf3;
    }

    .result-section {
        display: flex;
        align-items: center;
        gap: 0.6rem;
        padding: 0.5rem 0.6rem;
        background: #161b22;
        border-radius: 4px;
    }

    .result-label {
        font-size: 0.75rem;
        color: #8b949e;
    }

    .demo-btn {
        background: #ff3e00;
        color: white;
        border: none;
        padding: 0.4rem 0.8rem;
        border-radius: 4px;
        font-size: 0.75rem;
        font-weight: 600;
    }

    .cursor-pointer {
        position: absolute;
        pointer-events: none;
        z-index: 200;
        transition: all 0.6s cubic-bezier(0.4, 0, 0.2, 1);
        filter: drop-shadow(0 2px 4px rgba(0, 0, 0, 0.5));
    }
</style>
