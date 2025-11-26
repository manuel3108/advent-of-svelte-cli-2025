<script lang="ts">
    import { onMount } from 'svelte';

    let showNext = $state(false);

    onMount(() => {
        const timer = setTimeout(() => {
            showNext = true;
        }, 1000);
        return () => clearTimeout(timer);
    });
</script>

<div class="calendar-container">
    <div class="calendar" class:flip={showNext}>
        <div class="calendar-front">
            <div class="month">December</div>
            <div class="day">2</div>
            <div class="year">2025</div>
            <div class="decoration">
                <span>❄️</span>
                <span>🎄</span>
                <span>❄️</span>
            </div>
        </div>
        <div class="calendar-back">
            <div class="month">December</div>
            <div class="day next">3</div>
            <div class="year">2025</div>
            <div class="decoration">
                <span>🎁</span>
                <span>⭐</span>
                <span>🎁</span>
            </div>
        </div>
    </div>
</div>

<style>
    .calendar-container {
        perspective: 1000px;
    }

    .calendar {
        position: relative;
        width: 160px;
        height: 200px;
        transform-style: preserve-3d;
        transition: transform 0.8s cubic-bezier(0.4, 0, 0.2, 1);
    }

    .calendar.flip {
        transform: rotateY(180deg);
    }

    .calendar-front,
    .calendar-back {
        position: absolute;
        inset: 0;
        display: flex;
        flex-direction: column;
        align-items: center;
        justify-content: center;
        gap: 0.25rem;
        background: linear-gradient(
            135deg,
            #1e3a5f 0%,
            #0f3460 50%,
            #16213e 100%
        );
        border-radius: 16px;
        border: 2px solid rgba(255, 255, 255, 0.1);
        box-shadow:
            0 20px 50px rgba(0, 0, 0, 0.5),
            inset 0 1px 0 rgba(255, 255, 255, 0.1);
        backface-visibility: hidden;
    }

    .calendar-back {
        transform: rotateY(180deg);
        background: linear-gradient(
            135deg,
            #2d5a3d 0%,
            #1a4d2e 50%,
            #0d3320 100%
        );
    }

    .month {
        font-size: 0.9rem;
        font-weight: 600;
        color: #ff3e00;
        text-transform: uppercase;
        letter-spacing: 0.15em;
    }

    .day {
        font-size: 4.5rem;
        font-weight: 800;
        color: #ffffff;
        line-height: 1;
        text-shadow: 0 4px 20px rgba(0, 0, 0, 0.3);
    }

    .day.next {
        color: #64ffda;
        text-shadow: 0 0 30px rgba(100, 255, 218, 0.5);
    }

    .year {
        font-size: 0.8rem;
        color: #8892b0;
        font-weight: 500;
    }

    .decoration {
        display: flex;
        gap: 0.5rem;
        margin-top: 0.5rem;
        font-size: 1.25rem;
    }
</style>
