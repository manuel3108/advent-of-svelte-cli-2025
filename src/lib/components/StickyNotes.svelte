<script lang="ts">
    let { flags, variant = 'flags' } = $props<{
        flags: string[];
        variant?: 'flags' | 'packages';
    }>();

    const colors = [
        { bg: 'rgba(255, 62, 0, 0.15)', border: 'rgba(255, 62, 0, 0.4)' },
        { bg: 'rgba(100, 255, 218, 0.1)', border: 'rgba(100, 255, 218, 0.3)' },
        { bg: 'rgba(247, 147, 26, 0.15)', border: 'rgba(247, 147, 26, 0.4)' },
        { bg: 'rgba(139, 92, 246, 0.15)', border: 'rgba(139, 92, 246, 0.4)' },
    ];
</script>

<div class="sticky-notes">
    {#each flags as flag, i}
        <div
            class="note"
            class:package={variant === 'packages'}
            style="
				background: {colors[i % colors.length].bg};
				border-color: {colors[i % colors.length].border};
				transform: rotate({(i - 1) * 3}deg);
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
        gap: 0.75rem;
        padding: 0.75rem;
    }

    .note {
        padding: 0.6rem 1rem;
        border-radius: 8px;
        border: 1px solid;
        font-family: 'JetBrains Mono', monospace;
        font-size: 0.85rem;
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
