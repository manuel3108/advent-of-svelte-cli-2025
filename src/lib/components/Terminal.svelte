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

    // Insert word break opportunities before slashes and quotes for better line breaking
    function formatForDisplay(text: string): string {
        return text.replace(/\//g, '<wbr>/').replace(/"/g, '<wbr>"');
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
                displayedText = formatForDisplay(
                    baseText + text.slice(0, index)
                );
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
            displayedText = formatForDisplay(fullCommand);
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
                        // Phase 3: Paste URL (after toast shows for a moment)
                        scheduleTimeout(() => {
                            displayedText = formatForDisplay(
                                actualPrefix + pasteUrl
                            );
                            // Phase 4: Hide toast after URL is pasted
                            scheduleTimeout(() => {
                                showPasteToast = false;
                                // Phase 5: Type suffix if exists
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
                            }, 500);
                        }, 400);
                    }, 300);
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
    let initialized = false;
    let hasAnimated = false;
    $effect(() => {
        if (isVisible && !wasVisible && initialized) {
            hasAnimated = true;
            runAnimation();
        }
        // Only reset hasAnimated when fully leaving view
        if (!isVisible && wasVisible) {
            hasAnimated = false;
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

        // Delay observing to skip the initial intersection callback
        // that fires immediately when observing
        requestAnimationFrame(() => {
            observer.observe(terminalEl);
            // Mark as initialized after a frame to skip initial state
            requestAnimationFrame(() => {
                initialized = true;
            });
        });

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
            <span class="command-text"
                >{@html displayedText}{#if !animationComplete}<span
                        class="cursor"
                        class:visible={showCursor}>▌</span
                    >{/if}</span
            >
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
        width: 100%;
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
        font-size: 0.95rem;
        color: #8b949e;
        font-weight: 500;
    }

    .terminal-body {
        padding: 1.2rem;
        min-height: 3rem;
        position: relative;
    }

    .terminal-line {
        display: flex;
        align-items: baseline;
        gap: 0.5rem;
        text-align: left;
    }

    .prompt {
        color: #64ffda;
        font-weight: 600;
        font-size: 1.1rem;
        flex-shrink: 0;
    }

    .command-text {
        color: #e6edf3;
        font-size: 1rem;
        word-break: break-word;
        overflow-wrap: anywhere;
        flex: 1;
    }

    .cursor {
        color: #64ffda;
        font-size: 1.1rem;
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
        background: #238636;
        border: none;
        padding: 0.6rem 1.2rem;
        border-radius: 8px;
        display: flex;
        align-items: center;
        gap: 0.5rem;
        color: white;
        font-size: 1rem;
        font-weight: 600;
        animation: toastPop 0.3s ease-out;
        box-shadow: 0 4px 20px rgba(0, 0, 0, 0.4);
        z-index: 20;
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
