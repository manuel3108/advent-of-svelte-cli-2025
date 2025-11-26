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
    let containerEl: HTMLDivElement | null = null;
    let timeouts: ReturnType<typeof setTimeout>[] = [];
    let observer: IntersectionObserver | null = null;
    let isVisible = false;
    let hasStarted = false;

    // Derived - check if we're in paste-then-type mode
    const isPasteTypingMode = Boolean(pasteUrl && commandPrefix);

    function addTimeout(fn: () => void, delay: number) {
        const t = setTimeout(fn, delay);
        timeouts.push(t);
        return t;
    }

    function clearAllTimeouts() {
        timeouts.forEach(t => clearTimeout(t));
        timeouts = [];
    }

    function startTyping() {
        displayedCommand = '';
        let i = 0;

        function typeChar() {
            if (i < command.length && isVisible) {
                displayedCommand = command.slice(0, i + 1);
                i++;
                addTimeout(typeChar, 80 + Math.random() * 60);
            }
        }
        addTimeout(typeChar, 100);
    }

    function doTypeThenPaste() {
        displayedCommand = '';

        // Step 1: Type the prefix first
        let prefixI = 0;
        function typePrefixChar() {
            if (prefixI < commandPrefix.length && isVisible) {
                displayedCommand = commandPrefix.slice(0, prefixI + 1);
                prefixI++;
                addTimeout(typePrefixChar, 80 + Math.random() * 60);
            } else if (isVisible) {
                // Step 2: Paste the URL with toast
                addTimeout(() => {
                    showPasteToast = true;
                    displayedCommand = commandPrefix + pasteUrl;

                    // Step 3: Hide toast and type suffix
                    addTimeout(() => {
                        showPasteToast = false;

                        if (commandSuffix) {
                            let suffixI = 0;
                            function typeSuffixChar() {
                                if (suffixI < commandSuffix.length && isVisible) {
                                    displayedCommand = commandPrefix + pasteUrl + commandSuffix.slice(0, suffixI + 1);
                                    suffixI++;
                                    addTimeout(typeSuffixChar, 80 + Math.random() * 60);
                                }
                            }
                            addTimeout(typeSuffixChar, 200);
                        }
                    }, 800);
                }, 300);
            }
        }
        addTimeout(typePrefixChar, 100);
    }

    function doPaste() {
        showPasteToast = true;
        displayedCommand = command;
        addTimeout(() => {
            showPasteToast = false;
        }, 800);
    }

    function stopTyping() {
        clearAllTimeouts();
        displayedCommand = '';
        showPasteToast = false;
        hasStarted = false;
    }

    function handleVisibility(visible: boolean) {
        if (visible && !hasStarted) {
            isVisible = true;
            hasStarted = true;
            clearAllTimeouts();
            displayedCommand = '';
            
            if (isPasteTypingMode) {
                addTimeout(doTypeThenPaste, pasteDelay || delayStart);
            } else if (pasteMode) {
                addTimeout(doPaste, pasteDelay || delayStart);
            } else if (typing) {
                addTimeout(startTyping, delayStart);
            }
        } else if (!visible && hasStarted) {
            isVisible = false;
            stopTyping();
        }
    }

    onMount(() => {
        if (!typing && !pasteMode && !isPasteTypingMode) {
            displayedCommand = command;
            return;
        }

        observer = new IntersectionObserver(
            (entries) => {
                entries.forEach((entry) => {
                    handleVisibility(entry.isIntersecting && entry.intersectionRatio >= 0.5);
                });
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
        clearAllTimeouts();
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
                    : command}<span class="cursor"></span></span
            >
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
        min-height: 90px;
    }

    .line {
        display: flex;
        align-items: flex-start;
        gap: 0.5rem;
        text-align: left;
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
        text-align: left;
    }

    .cursor {
        display: inline-block;
        width: 10px;
        height: 1.1em;
        background: #64ffda;
        animation: blink 1s step-end infinite;
        vertical-align: text-bottom;
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
