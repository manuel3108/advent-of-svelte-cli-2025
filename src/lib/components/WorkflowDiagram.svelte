<script lang="ts">
</script>

<div class="workflow">
	<div class="row">
		<div class="step">
			<div class="icon">🚀</div>
			<div class="label">create</div>
		</div>
		<div class="arrow">→</div>
		<div class="step">
			<div class="icon">➕</div>
			<div class="label">add</div>
		</div>
	</div>
	<div class="arrow-down">↓</div>
	<div class="row">
		<div class="step">
			<div class="icon">🎮</div>
			<div class="label">playground</div>
		</div>
		<div class="arrow">←</div>
		<div class="step">
			<div class="icon">🔄</div>
			<div class="label">migrate</div>
		</div>
	</div>
</div>

<style>
	.workflow {
		display: flex;
		flex-direction: column;
		align-items: center;
		gap: 0.75rem;
		padding: 1.5rem;
		background: rgba(255, 255, 255, 0.03);
		border-radius: 16px;
		border: 1px solid rgba(255, 255, 255, 0.1);
	}

	.row {
		display: flex;
		align-items: center;
		gap: 0.6rem;
	}

	.step {
		display: flex;
		flex-direction: column;
		align-items: center;
		gap: 0.4rem;
		padding: 0.75rem 1rem;
		background: linear-gradient(135deg, rgba(255, 62, 0, 0.1) 0%, rgba(255, 107, 53, 0.05) 100%);
		border-radius: 12px;
		border: 1px solid rgba(255, 62, 0, 0.3);
		min-width: 80px;
		transition: transform 0.3s ease, box-shadow 0.3s ease;
	}

	.step:hover {
		transform: translateY(-2px);
		box-shadow: 0 10px 30px rgba(255, 62, 0, 0.2);
	}

	.icon {
		font-size: 1.8rem;
	}

	.label {
		font-size: 0.8rem;
		font-weight: 600;
		color: #ff3e00;
		font-family: 'JetBrains Mono', monospace;
	}

	.arrow {
		color: #64ffda;
		font-size: 1.4rem;
		font-weight: bold;
		opacity: 0.7;
	}

	.arrow-down {
		color: #64ffda;
		font-size: 1.4rem;
		font-weight: bold;
		opacity: 0.7;
	}
</style>
