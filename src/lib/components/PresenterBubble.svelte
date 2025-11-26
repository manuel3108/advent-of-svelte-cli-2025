<script lang="ts">
</script>

<div class="presenter-bubble">
    <div class="bubble-ring"></div>
    <div class="bubble-content">
        <div class="avatar-placeholder">
            <span class="avatar-icon">👤</span>
        </div>
    </div>
    <div class="bubble-glow"></div>
</div>

<style>
    .presenter-bubble {
        position: absolute;
        bottom: 1rem;
        right: 1rem;
        width: 70px;
        height: 70px;
        z-index: 1000;
    }

    .bubble-ring {
        position: absolute;
        inset: -3px;
        border-radius: 50%;
        background: linear-gradient(
            135deg,
            #ff3e00 0%,
            #ff6b35 50%,
            #64ffda 100%
        );
        animation: rotate 4s linear infinite;
    }

    .bubble-content {
        position: absolute;
        inset: 3px;
        border-radius: 50%;
        background: linear-gradient(135deg, #1a1a2e 0%, #16213e 100%);
        display: flex;
        align-items: center;
        justify-content: center;
        overflow: hidden;
    }

    .avatar-placeholder {
        width: 100%;
        height: 100%;
        display: flex;
        align-items: center;
        justify-content: center;
        background: linear-gradient(135deg, #0f3460 0%, #1a1a2e 100%);
    }

    .avatar-icon {
        font-size: 2rem;
        opacity: 0.5;
    }

    .bubble-glow {
        position: absolute;
        inset: -10px;
        border-radius: 50%;
        background: radial-gradient(
            circle,
            rgba(255, 62, 0, 0.3) 0%,
            transparent 70%
        );
        animation: pulse 2s ease-in-out infinite;
        z-index: -1;
    }

    @keyframes rotate {
        from {
            transform: rotate(0deg);
        }
        to {
            transform: rotate(360deg);
        }
    }

    @keyframes pulse {
        0%,
        100% {
            opacity: 0.5;
            transform: scale(1);
        }
        50% {
            opacity: 0.8;
            transform: scale(1.1);
        }
    }
</style>
