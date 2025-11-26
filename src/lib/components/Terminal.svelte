<script lang="ts">
    import { onMount } from 'svelte';

    let { command, typing = false } = $props();

    let displayedCommand = $state('');
    let isTyping = $state(false);
    let containerEl: HTMLDivElement;

    function startTyping() {
        isTyping = true;
        displayedCommand = '';
        let i = 0;

        function typeChar() {
            if (i < command.length) {
                displayedCommand = command.slice(0, i + 1);
                i++;
                setTimeout(typeChar, 50 + Math.random() * 30);
            } else {
                isTyping = false;
            }
        }
        typeChar();
    }

    onMount(() => {
        if (!typing) {
            displayedCommand = command;
            return;
        }

        // Use IntersectionObserver to detect when slide is visible
        const observer = new IntersectionObserver(
            (entries) => {
                entries.forEach((entry) => {
                    if (entry.isIntersecting) {
                        // Reset and start typing when slide becomes visible
                        displayedCommand = '';
                        isTyping = false;
                        setTimeout(startTyping, 100);
                    }
                });
            },
            { threshold: 0.5 }
        );

        observer.observe(containerEl);

        return () => observer.disconnect();
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
            <span class="command">{typing ? displayedCommand : command}</span>
            <span class="cursor"></span>
        </div>
    </div>
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
        min-height: 50px;
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
</style>
