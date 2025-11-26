<script lang="ts">
    import { onMount, onDestroy } from 'svelte';

    // pasteUrl: the URL to paste first
    // commandPrefix: what to type before the URL (e.g. 'npx sv create --from-playground="')
    // commandSuffix: what to type after the URL (e.g. '"')
    let {
        command = '',
        typing = false,
        delayStart = 0,
        pasteMode = false,
        pasteDelay = 0,
        pasteUrl = '',
        commandPrefix = '',
        commandSuffix = '',
    } = $props();

    let displayedCommand = $state('');
    let showPasteToast = $state(false);
    let containerEl: HTMLDivElement | undefined;
    let typingTimeout: ReturnType<typeof setTimeout> | undefined;
    let observer: IntersectionObserver | undefined;
    let isVisible = false;

    // Derived - check if we're in paste-then-type mode
    let isPasteTypingMode = $derived(Boolean(pasteUrl && commandPrefix));

    function startTyping() {
        displayedCommand = '';
        let i = 0;

        function typeChar() {
            if (i < command.length && isVisible) {
                displayedCommand = command.slice(0, i + 1);
                i++;
                typingTimeout = setTimeout(typeChar, 80 + Math.random() * 60);
            }
        }
        typingTimeout = setTimeout(typeChar, 100);
    }

    function doPasteAndType() {
        // Step 1: Show pasted URL with toast
        showPasteToast = true;
        displayedCommand = pasteUrl;

        // Step 2: Hide toast after 800ms
        typingTimeout = setTimeout(() => {
            showPasteToast = false;

            // Step 3: Start typing prefix before the URL
            let prefixI = 0;
            function typePrefixChar() {
                if (prefixI < commandPrefix.length && isVisible) {
                    displayedCommand =
                        commandPrefix.slice(0, prefixI + 1) + pasteUrl;
                    prefixI++;
                    typingTimeout = setTimeout(
                        typePrefixChar,
                        80 + Math.random() * 60
                    );
                } else if (commandSuffix) {
                    // Step 4: Type suffix after the URL
                    let suffixI = 0;
                    function typeSuffixChar() {
                        if (suffixI < commandSuffix.length && isVisible) {
                            displayedCommand =
                                commandPrefix +
                                pasteUrl +
                                commandSuffix.slice(0, suffixI + 1);
                            suffixI++;
                            typingTimeout = setTimeout(
                                typeSuffixChar,
                                80 + Math.random() * 60
                            );
                        }
                    }
                    typingTimeout = setTimeout(typeSuffixChar, 100);
                }
            }
            typingTimeout = setTimeout(typePrefixChar, 300);
        }, 800);
    }

    function doPaste() {
        showPasteToast = true;
        displayedCommand = command;
        typingTimeout = setTimeout(() => {
            showPasteToast = false;
        }, 800);
    }

    function stopTyping() {
        if (typingTimeout) {
            clearTimeout(typingTimeout);
            typingTimeout = undefined;
        }
        displayedCommand = '';
        showPasteToast = false;
    }

    function handleVisibility(visible: boolean) {
        if (visible && !isVisible) {
            isVisible = true;
            stopTyping();
            if (pasteUrl && commandPrefix) {
                typingTimeout = setTimeout(doPasteAndType, pasteDelay);
            } else if (pasteMode) {
                typingTimeout = setTimeout(doPaste, pasteDelay);
            } else if (typing) {
                typingTimeout = setTimeout(startTyping, delayStart);
            }
        } else if (!visible && isVisible) {
            isVisible = false;
            stopTyping();
        }
    }

    onMount(() => {
        const hasPasteTyping = pasteUrl && commandPrefix;

        if (!typing && !pasteMode && !hasPasteTyping) {
            displayedCommand = command;
            return;
        }

        observer = new IntersectionObserver(
            (entries) => {
                const entry = entries[0];
                if (entry) {
                    handleVisibility(
                        entry.isIntersecting && entry.intersectionRatio >= 0.5
                    );
                }
            },
            { threshold: [0, 0.5, 1] }
        );

        if (containerEl) {
            observer.observe(containerEl);
        }
    });

    onDestroy(() => {
        if (observer) {
            observer.disconnect();
        }
        stopTyping();
    });
</script>

<div class="terminal" bind:this={containerEl}>
    <div class="terminal-header">
        <div class="dots">
            <span class="dot red"></span>
            <span class="dot yellow"></span>
            <span class="dot green"></span>
        </div>
    </div>
    <div class="terminal-body">
        <div class="line">
            <span class="prompt">$</span>
            <span class="command"
                >{typing || pasteMode || isPasteTypingMode
                    ? displayedCommand
                    : command}</span
            >
            <span class="cursor"></span>
        </div>
    </div>
    {#if showPasteToast}
        <div class="paste-toast">📋 Pasted!</div>
    {/if}
</div>

<style>
    .terminal {
        width: 92%;
        background: #0d1117;
        border-radius: 12px;
        overflow: hidden;
        box-shadow:
            0 10px 40px rgba(0, 0, 0, 0.6),
            0 0 0 1px rgba(255, 255, 255, 0.1);
        font-family: 'JetBrains Mono', 'Fira Code', 'Consolas', monospace;
    }

    .terminal-header {
        display: flex;
        align-items: center;
        padding: 0.6rem 0.8rem;
        background: #161b22;
        border-bottom: 1px solid #30363d;
    }

    .dots {
        display: flex;
        gap: 0.4rem;
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

    .terminal-body {
        padding: 1rem;
        min-height: 70px;
    }

    .line {
        display: flex;
        align-items: center;
        gap: 0.5rem;
    }

    .prompt {
        color: #64ffda;
        font-weight: bold;
        font-size: 1rem;
    }

    .command {
        color: #e6edf3;
        font-size: 0.9rem;
        word-break: break-all;
        line-height: 1.4;
    }

    .cursor {
        display: inline-block;
        width: 10px;
        height: 1.2em;
        background: #64ffda;
        animation: blink 1s step-end infinite;
        margin-left: 2px;
    }

    @keyframes blink {
        0%,
        50% {
            opacity: 1;
        }
        51%,
        100% {
            opacity: 0;
        }
    }

    .terminal {
        position: relative;
    }

    .paste-toast {
        position: absolute;
        top: 50%;
        left: 50%;
        transform: translate(-50%, -50%);
        background: #238636;
        color: white;
        padding: 0.5rem 1rem;
        border-radius: 8px;
        font-size: 0.85rem;
        font-weight: 600;
        z-index: 100;
        animation: fadeInPaste 0.2s ease;
    }

    @keyframes fadeInPaste {
        from {
            opacity: 0;
            transform: translate(-50%, -50%) scale(0.8);
        }
        to {
            opacity: 1;
            transform: translate(-50%, -50%) scale(1);
        }
    }
</style>
