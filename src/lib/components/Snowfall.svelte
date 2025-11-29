<script lang="ts">
    import { onMount } from 'svelte';

    let { density = 30 } = $props();

    interface Snowflake {
        id: number;
        x: number;
        y: number;
        size: number;
        speed: number;
        opacity: number;
        wobble: number;
    }

    let snowflakes = $state<Snowflake[]>([]);

    onMount(() => {
        snowflakes = Array.from({ length: density }, (_, i) => ({
            id: i,
            x: Math.random() * 100,
            y: 0, // All snowflakes start at top, animation handles vertical position
            size: Math.random() * 10 + 6,
            speed: Math.random() * 4 + 6,
            opacity: Math.random() * 0.7 + 0.3,
            wobble: Math.random() * 360,
        }));
    });
</script>

<div class="snowfall">
    {#each snowflakes as flake (flake.id)}
        <div
            class="snowflake"
            style="
				left: {flake.x}%;
				top: {flake.y}%;
				width: {flake.size}px;
				height: {flake.size}px;
				opacity: {flake.opacity};
				animation-duration: {flake.speed}s;
				animation-delay: -{Math.random() * 8}s;
			"
        ></div>
    {/each}
</div>

<style>
    .snowfall {
        position: absolute;
        inset: 0;
        overflow: hidden;
        pointer-events: none;
        z-index: 1;
    }

    .snowflake {
        position: absolute;
        background: radial-gradient(circle, white 0%, transparent 70%);
        border-radius: 50%;
        animation: fall linear infinite;
        filter: blur(0.5px);
    }

    @keyframes fall {
        0% {
            transform: translateY(-20px) translateX(0) rotate(0deg);
        }
        25% {
            transform: translateY(25vh) translateX(10px) rotate(90deg);
        }
        50% {
            transform: translateY(50vh) translateX(-10px) rotate(180deg);
        }
        75% {
            transform: translateY(75vh) translateX(10px) rotate(270deg);
        }
        100% {
            transform: translateY(110vh) translateX(0) rotate(360deg);
        }
    }
</style>
