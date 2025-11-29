<script lang="ts">
    import { onMount, tick } from 'svelte';

    interface Props {
        /** The full command to display (used when not in animated mode, or as the typed text in typing mode) */
        command?: string;
        /** Enable character-by-character typing animation */
        typing?: boolean;
        /** Delay in ms before animation starts */
        delay?: number;
        /** @deprecated Use `delay` instead */
        pasteDelay?: number;
        /** @deprecated Use `delay` instead */
        delayStart?: number;
        /** Show a "pasted" toast animation instead of typing */
        paste?: boolean;
        /** @deprecated Use `paste` instead */
        pasteMode?: boolean;
        /** URL to paste (enables paste-typing mode when combined with prefix) */
        pasteUrl?: string;
        /** Text to type before pasting the URL */
        prefix?: string;
        /** @deprecated Use `prefix` instead */
        commandPrefix?: string;
        /** Text to type after pasting the URL */
        suffix?: string;
        /** @deprecated Use `suffix` instead */
        commandSuffix?: string;
    }

    let {
        command = '',
        typing = false,
        delay = 0,
        pasteDelay,
        delayStart,
        paste = false,
        pasteMode,
        pasteUrl = '',
        prefix = '',
        commandPrefix,
        suffix = '',
        commandSuffix,
    }: Props = $props();

    // Handle deprecated props
    const actualDelay = pasteDelay ?? delayStart ?? delay;
    const actualPaste = pasteMode ?? paste;
    const actualPrefix = commandPrefix ?? prefix;
    const actualSuffix = commandSuffix ?? suffix;

    // Determine if this is a paste-url mode (--from-playground use case)
    const isPasteUrlMode = pasteUrl !== '';

    // Build the full command text
    const fullCommand = isPasteUrlMode
        ? `${actualPrefix}${pasteUrl}${actualSuffix}`
        : command;

    // Animation state
    let terminalEl: HTMLDivElement;
    let isVisible = $state(false);
    let displayedText = $state('');
    let showCursor = $state(true);
    let showPasteToast = $state(false);
    let animationComplete = $state(false);
    let timeoutIds: number[] = [];

    function clearTimeouts() {
        timeoutIds.forEach((id) => clearTimeout(id));
        timeoutIds = [];
    }

    function scheduleTimeout(fn: () => void, ms: number) {
        const id = setTimeout(fn, ms) as unknown as number;
        timeoutIds.push(id);
    }

    function typeText(text: string, baseText: string, onComplete: () => void) {
        let index = 0;

        function typeNext() {
            if (index < text.length) {
                index++;
                displayedText = baseText + text.slice(0, index);
                scheduleTimeout(typeNext, 50 + Math.random() * 30);
            } else {
                onComplete();
            }
        }

        typeNext();
    }

    function runAnimation() {
        clearTimeouts();
        displayedText = '';
        showPasteToast = false;
        animationComplete = false;

        if (!typing && !isPasteUrlMode) {
            displayedText = fullCommand;
            animationComplete = true;
            return;
        }

        scheduleTimeout(() => {
            if (isPasteUrlMode) {
                // Phase 1: Type prefix
                typeText(actualPrefix, '', () => {
                    // Phase 2: Show paste toast
                    scheduleTimeout(() => {
                        showPasteToast = true;
                        // Phase 3: Paste URL
                        scheduleTimeout(() => {
                            displayedText = actualPrefix + pasteUrl;
                            showPasteToast = false;

                            // Phase 4: Type suffix if exists
                            if (actualSuffix) {
                                scheduleTimeout(() => {
                                    typeText(
                                        actualSuffix,
                                        actualPrefix + pasteUrl,
                                        () => {
                                            animationComplete = true;
                                        }
                                    );
                                }, 100);
                            } else {
                                animationComplete = true;
                            }
                        }, 600);
                    }, 200);
                });
            } else {
                typeText(fullCommand, '', () => {
                    animationComplete = true;
                });
            }
        }, actualDelay);
    }

    // React to visibility changes - only trigger on becoming visible
    let wasVisible = false;
    $effect(() => {
        if (isVisible && !wasVisible) {
            runAnimation();
        }
        wasVisible = isVisible;
    });

    // Cursor blink
    $effect(() => {
        const interval = setInterval(() => {
            showCursor = !showCursor;
        }, 530);
        return () => clearInterval(interval);
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

        observer.observe(terminalEl);

        return () => {
            observer.disconnect();
            clearTimeouts();
        };
    });
</script>

<div class="terminal" bind:this={terminalEl}>
    <div class="terminal-header">
        <div class="terminal-buttons">
            <span class="btn close"></span>
            <span class="btn minimize"></span>
            <span class="btn maximize"></span>
        </div>
        <span class="terminal-title">Terminal</span>
    </div>
    <div class="terminal-body">
        <div class="terminal-line">
            <span class="prompt">$</span>
            <span class="command-text">{@html displayedText}</span>
            {#if !animationComplete}
                <span class="cursor" class:visible={showCursor}>▌</span>
            {/if}
        </div>
        {#if showPasteToast}
            <div class="paste-toast">
                <span class="paste-icon">📋</span>
                <span>Pasted!</span>
            </div>
        {/if}
    </div>
</div>

<style>
    .terminal {
        width: 92%;
        background: #0d1117;
        border-radius: 12px;
        overflow: hidden;
        border: 1px solid #30363d;
        font-family: 'JetBrains Mono', monospace;
        box-shadow: 0 8px 32px rgba(0, 0, 0, 0.4);
        position: relative;
        z-index: 10;
    }

    .terminal-header {
        display: flex;
        align-items: center;
        padding: 0.6rem 0.9rem;
        background: #161b22;
        border-bottom: 1px solid #30363d;
        position: relative;
    }

    .terminal-buttons {
        display: flex;
        gap: 0.4rem;
    }

    .btn {
        width: 12px;
        height: 12px;
        border-radius: 50%;
    }

    .btn.close {
        background: #ff5f56;
    }

    .btn.minimize {
        background: #ffbd2e;
    }

    .btn.maximize {
        background: #27ca40;
    }

    .terminal-title {
        position: absolute;
        left: 50%;
        transform: translateX(-50%);
        font-size: 0.75rem;
        color: #8b949e;
        font-weight: 500;
    }

    .terminal-body {
        padding: 1rem;
        min-height: 2.5rem;
        position: relative;
    }

    .terminal-line {
        display: flex;
        align-items: center;
        gap: 0.5rem;
        flex-wrap: wrap;
    }

    .prompt {
        color: #64ffda;
        font-weight: 600;
        font-size: 0.9rem;
    }

    .command-text {
        color: #e6edf3;
        font-size: 0.85rem;
        word-break: break-all;
    }

    .cursor {
        color: #64ffda;
        font-size: 0.9rem;
        opacity: 0;
        display: inline-block;
        width: 0.5em;
    }

    .cursor.visible {
        opacity: 1;
    }

    .paste-toast {
        position: absolute;
        top: 50%;
        left: 50%;
        transform: translate(-50%, -50%);
        background: rgba(100, 255, 218, 0.15);
        border: 1px solid rgba(100, 255, 218, 0.4);
        padding: 0.5rem 1rem;
        border-radius: 8px;
        display: flex;
        align-items: center;
        gap: 0.5rem;
        color: #64ffda;
        font-size: 0.85rem;
        font-weight: 600;
        animation: toastPop 0.3s ease-out;
        box-shadow: 0 4px 20px rgba(100, 255, 218, 0.2);
    }

    .paste-icon {
        font-size: 1rem;
    }

    @keyframes toastPop {
        0% {
            opacity: 0;
            transform: translate(-50%, -50%) scale(0.8);
        }
        100% {
            opacity: 1;
            transform: translate(-50%, -50%) scale(1);
        }
    }
</style>
