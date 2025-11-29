<script lang="ts">
    import { onMount } from 'svelte';

    interface Props {
        flags: string[];
        variant?: 'flags' | 'packages';
        /** Wait for keypress before showing notes */
        waitForKeypress?: boolean;
        /** Key to reset state and prepare for new animation */
        slideKey?: number;
        /** Whether the component is ready to accept keypresses (for multi-phase animations) */
        readyForKeypress?: boolean;
    }

    let {
        flags,
        variant = 'flags',
        waitForKeypress = false,
        slideKey = 0,
        readyForKeypress = true,
    }: Props = $props();

    const colors = [
        { bg: 'rgba(255, 62, 0, 0.15)', border: 'rgba(255, 62, 0, 0.4)' }, // Svelte orange
        { bg: 'rgba(100, 255, 218, 0.1)', border: 'rgba(100, 255, 218, 0.3)' }, // Teal/cyan
        { bg: 'rgba(247, 147, 26, 0.15)', border: 'rgba(247, 147, 26, 0.4)' }, // Amber
        { bg: 'rgba(139, 92, 246, 0.15)', border: 'rgba(139, 92, 246, 0.4)' }, // Purple
        { bg: 'rgba(236, 72, 153, 0.15)', border: 'rgba(236, 72, 153, 0.4)' }, // Pink
        { bg: 'rgba(34, 197, 94, 0.15)', border: 'rgba(34, 197, 94, 0.4)' }, // Green
        { bg: 'rgba(59, 130, 246, 0.15)', border: 'rgba(59, 130, 246, 0.4)' }, // Blue
        { bg: 'rgba(251, 191, 36, 0.15)', border: 'rgba(251, 191, 36, 0.4)' }, // Yellow
    ];

    let containerEl: HTMLDivElement;
    let isVisible = $state(false);
    let showNotes = $state(false);
    let previousSlideKey = slideKey;
    let keypressTriggered = $state(false);

    // Reset state when slideKey changes
    $effect(() => {
        if (slideKey !== previousSlideKey) {
            previousSlideKey = slideKey;
            showNotes = false;
            keypressTriggered = false;
        }
    });

    // Track readyForKeypress from previous frame to avoid race conditions
    let wasReadyLastFrame = $state(false);

    $effect(() => {
        // Update wasReadyLastFrame after a microtask to capture "previous" state
        const currentReady = readyForKeypress;
        requestAnimationFrame(() => {
            wasReadyLastFrame = currentReady;
        });
    });

    // Handle keypress for triggering notes
    function handleKeypress(event: KeyboardEvent) {
        // Use Numpad 0 as trigger (doesn't conflict with Reveal.js navigation)
        if (
            event.key === '0' &&
            event.location === 3 &&
            !keypressTriggered &&
            isVisible &&
            waitForKeypress &&
            wasReadyLastFrame // Use the value from before this keypress could trigger any changes
        ) {
            event.preventDefault();
            event.stopPropagation();
            keypressTriggered = true;
            showNotes = true;
        }
    }

    // React to visibility changes
    let wasVisible = false;
    let initialized = false;
    $effect(() => {
        if (isVisible && !wasVisible && initialized) {
            if (!waitForKeypress) {
                showNotes = true;
            } else {
                // Add keypress listener when becoming visible
                window.addEventListener('keydown', handleKeypress);
            }
        }
        if (!isVisible && wasVisible) {
            showNotes = false;
            keypressTriggered = false;
            // Remove keypress listener when leaving view
            if (waitForKeypress) {
                window.removeEventListener('keydown', handleKeypress);
            }
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

<div class="sticky-notes" bind:this={containerEl} class:hidden={!showNotes}>
    {#each flags as flag, i}
        <div
            class="note"
            class:package={variant === 'packages'}
            style="
                background: {colors[i % colors.length].bg};
                border-color: {colors[i % colors.length].border};
                transform: rotate({((i % 3) - 1) * 2.5}deg);
                animation-delay: {i * 0.1}s;
            "
        >
            <span class="flag-text">{flag}</span>
        </div>
    {/each}
</div>

<style>
    .sticky-notes {
        display: flex;
        flex-wrap: wrap;
        justify-content: center;
        gap: 1rem;
        padding: 1rem;
    }

    .sticky-notes.hidden {
        visibility: hidden;
    }

    .sticky-notes.hidden .note {
        animation: none;
    }

    .note {
        padding: 0.8rem 1.2rem;
        border-radius: 8px;
        border: 1px solid;
        font-family: 'JetBrains Mono', monospace;
        font-size: 1rem;
        font-weight: 600;
        color: #e6edf3;
        box-shadow: 0 4px 15px rgba(0, 0, 0, 0.3);
        animation: popIn 0.4s ease-out backwards;
        transition: transform 0.2s ease;
    }

    .note:hover {
        transform: scale(1.05) rotate(0deg) !important;
    }

    .note.package {
        border-radius: 20px;
    }

    .flag-text {
        text-shadow: 0 1px 2px rgba(0, 0, 0, 0.3);
    }

    @keyframes popIn {
        0% {
            opacity: 0;
            transform: scale(0.8) rotate(0deg);
        }
        100% {
            opacity: 1;
        }
    }
</style>
