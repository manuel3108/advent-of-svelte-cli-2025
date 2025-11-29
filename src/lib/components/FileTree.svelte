<script lang="ts">
</script>

<div class="file-tree">
    <div class="tree-header">
        <span class="folder-icon">📁</span>
        <span class="folder-name">my-svelte-project</span>
    </div>
    <div class="tree-content">
        <div class="tree-item">
            <span class="icon">📁</span>
            <span class="name">src</span>
        </div>
        <div class="tree-item indent-1">
            <span class="icon">📁</span>
            <span class="name">routes</span>
        </div>
        <div class="tree-item indent-2">
            <span class="icon file">📄</span>
            <span class="name">+page.svelte</span>
            <span class="badge migrate">migrate</span>
        </div>
        <div class="tree-item indent-1">
            <span class="icon file">📄</span>
            <span class="name">app.html</span>
        </div>
        <div class="tree-item">
            <span class="icon file">📄</span>
            <span class="name">svelte.config.js</span>
            <span class="badge migrate">migrate</span>
        </div>
        <div class="tree-item">
            <span class="icon file">📄</span>
            <span class="name">package.json</span>
        </div>
    </div>
</div>

<style>
    .file-tree {
        width: 100%;
        background: #0d1117;
        border-radius: 12px;
        overflow: hidden;
        border: 1px solid #30363d;
        font-family: 'JetBrains Mono', monospace;
        font-size: 0.8rem;
    }

    .tree-header {
        display: flex;
        align-items: center;
        gap: 0.5rem;
        padding: 0.6rem 0.9rem;
        background: #161b22;
        border-bottom: 1px solid #30363d;
    }

    .folder-icon {
        font-size: 1rem;
    }

    .folder-name {
        color: #e6edf3;
        font-weight: 600;
    }

    .tree-content {
        padding: 0.6rem;
    }

    .tree-item {
        display: flex;
        align-items: center;
        gap: 0.5rem;
        padding: 0.35rem 0.5rem;
        border-radius: 6px;
        transition: background 0.2s ease;
    }

    .tree-item:hover {
        background: rgba(255, 255, 255, 0.05);
    }

    .indent-1 {
        padding-left: 1.4rem;
    }

    .indent-2 {
        padding-left: 2.4rem;
    }

    .icon {
        font-size: 0.9rem;
        opacity: 0.8;
    }

    .icon.file {
        opacity: 0.6;
    }

    .name {
        color: #8b949e;
    }

    .badge {
        margin-left: auto;
        padding: 0.15rem 0.4rem;
        border-radius: 10px;
        font-size: 0.6rem;
        font-weight: 600;
        text-transform: uppercase;
    }

    .badge.migrate {
        background: rgba(255, 62, 0, 0.2);
        color: #ff3e00;
        border: 1px solid rgba(255, 62, 0, 0.3);
    }
</style>
