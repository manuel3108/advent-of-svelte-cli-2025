<script lang="ts">
	// Simulates a Svelte playground interface - stacked vertically
</script>

<div class="playground">
	<!-- Files Section -->
	<div class="section">
		<div class="section-header">
			<span class="section-icon">📁</span>
			<span class="section-title">Files</span>
		</div>
		<div class="section-content files">
			<div class="file active">App.svelte</div>
			<div class="file">utils.js</div>
		</div>
	</div>

	<!-- Code Section -->
	<div class="section">
		<div class="section-header">
			<span class="section-icon">📝</span>
			<span class="section-title">Code</span>
		</div>
		<div class="section-content code">
			<div class="line"><span class="tag">&lt;script&gt;</span></div>
			<div class="line">  <span class="keyword">let</span> <span class="variable">count</span> = <span class="state">$state</span>(<span class="number">0</span>);</div>
			<div class="line"><span class="tag">&lt;/script&gt;</span></div>
			<div class="line"><span class="tag">&lt;button</span> <span class="attr">onclick</span>=<span class="string">{"{"}() =&gt; count++{"}"}</span><span class="tag">&gt;</span></div>
			<div class="line">  Clicks: <span class="string">{"{"}count{"}"}</span></div>
			<div class="line"><span class="tag">&lt;/button&gt;</span></div>
		</div>
	</div>

	<!-- Preview Section -->
	<div class="section">
		<div class="section-header">
			<span class="section-icon">▶️</span>
			<span class="section-title">Result</span>
		</div>
		<div class="section-content preview">
			<button class="demo-btn">Clicks: 0</button>
		</div>
	</div>

	<!-- URL Bar -->
	<div class="url-bar">
		<span class="url">svelte.dev/playground/abc123...</span>
	</div>
</div>

<style>
	.playground {
		width: 92%;
		background: #0d1117;
		border-radius: 12px;
		overflow: hidden;
		box-shadow: 
			0 10px 40px rgba(0, 0, 0, 0.6),
			0 0 0 1px rgba(255, 255, 255, 0.1);
		font-family: 'Inter', sans-serif;
	}

	.section {
		border-bottom: 1px solid #30363d;
	}

	.section:last-of-type {
		border-bottom: none;
	}

	.section-header {
		display: flex;
		align-items: center;
		gap: 0.4rem;
		padding: 0.4rem 0.6rem;
		background: #161b22;
		border-bottom: 1px solid #30363d;
	}

	.section-icon {
		font-size: 0.7rem;
	}

	.section-title {
		font-size: 0.65rem;
		font-weight: 600;
		color: #8b949e;
		text-transform: uppercase;
		letter-spacing: 0.05em;
	}

	.section-content {
		padding: 0.5rem 0.6rem;
	}

	.section-content.files {
		display: flex;
		gap: 0.4rem;
	}

	.file {
		font-size: 0.6rem;
		color: #8b949e;
		padding: 0.2rem 0.5rem;
		border-radius: 4px;
		background: #21262d;
	}

	.file.active {
		background: #ff3e00;
		color: white;
	}

	.section-content.code {
		font-family: 'JetBrains Mono', 'Fira Code', monospace;
		font-size: 0.55rem;
		line-height: 1.5;
		background: #0d1117;
	}

	.line {
		white-space: nowrap;
	}

	.tag { color: #7ee787; }
	.keyword { color: #ff7b72; }
	.variable { color: #79c0ff; }
	.state { color: #ffa657; }
	.number { color: #79c0ff; }
	.attr { color: #79c0ff; }
	.string { color: #a5d6ff; }

	.section-content.preview {
		display: flex;
		align-items: center;
		justify-content: center;
		padding: 0.6rem;
		background: #161b22;
	}

	.demo-btn {
		background: #ff3e00;
		color: white;
		border: none;
		padding: 0.4rem 0.8rem;
		border-radius: 6px;
		font-size: 0.65rem;
		font-weight: 600;
		cursor: pointer;
	}

	.url-bar {
		padding: 0.4rem 0.6rem;
		background: #161b22;
		border-top: 1px solid #30363d;
	}

	.url {
		font-size: 0.55rem;
		color: #64ffda;
		font-family: 'JetBrains Mono', monospace;
	}
</style>
