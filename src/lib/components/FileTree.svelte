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
        <div class="tree-item indent-2 has-badge">
            <span class="icon file">📄</span>
            <span class="name">+page.svelte</span>
        </div>
        <div class="tree-item indent-1">
            <span class="icon file">📄</span>
            <span class="name">app.html</span>
        </div>
        <div class="tree-item has-badge">
            <span class="icon file">📄</span>
            <span class="name">svelte.config.js</span>
        </div>
        <div class="tree-item">
            <span class="icon file">📄</span>
            <span class="name">package.json</span>
        </div>
    </div>
    <div class="badges-column">
        <div class="badge-row"></div>
        <div class="badge-row"></div>
        <div class="badge-row"><span class="badge migrate">migrate</span></div>
        <div class="badge-row"></div>
        <div class="badge-row"><span class="badge migrate">migrate</span></div>
        <div class="badge-row"></div>
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
        position: relative;
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
        padding-right: 6rem;
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
        flex-shrink: 0;
    }

    .icon.file {
        opacity: 0.6;
    }

    .name {
        color: #8b949e;
        text-align: left;
    }

    .badges-column {
        position: absolute;
        top: 0;
        right: 0.6rem;
        bottom: 0;
        display: flex;
        flex-direction: column;
        padding-top: calc(0.6rem + 0.9rem + 1rem + 1px);
    }

    .badge-row {
        height: calc(0.35rem * 2 + 1.2rem);
        display: flex;
        align-items: center;
        justify-content: flex-end;
    }

    .badge {
        padding: 0.25rem 0.6rem;
        border-radius: 4px;
        font-size: 0.75rem;
        font-weight: 700;
        text-transform: uppercase;
        text-align: center;
    }

    .badge.migrate {
        background: #ff3e00;
        color: white;
    }
</style>
