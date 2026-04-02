<script lang="ts">
	let activeItem = $state('home');

	const navItems = [
		{ id: 'home', label: 'HOME' },
		{ id: 'works', label: 'WORKS' },
		{ id: 'thoughts', label: 'THOUGHTS' },
		{ id: 'archive', label: 'ARCHIVE' }
	];

	function setActive(id: string) {
		activeItem = id;
	}
</script>

<nav class="navbar">
	{#each navItems as item, i}
		<button
			class="nav-item"
			class:active={activeItem === item.id}
			onclick={() => setActive(item.id)}
			style="animation-delay: {0.15 + i * 0.05}s"
		>
			<span class="nav-index">{String(i + 1).padStart(2, '0')}</span>
			<span class="nav-text">{item.label}</span>
			{#if activeItem === item.id}
				<span class="nav-dot"></span>
			{/if}
		</button>
	{/each}
</nav>

<style>
	.navbar {
		display: flex;
		flex-direction: column;
		padding: 0 2rem 2rem;
	}

	.nav-item {
		display: flex;
		align-items: center;
		gap: 1rem;
		padding: 1rem 0;
		background: none;
		border: none;
		border-bottom: 1px solid var(--color-border);
		cursor: pointer;
		font-family: var(--font-mono);
		color: var(--color-text-dim);
		text-align: left;
		position: relative;
		transition: all 0.2s ease;
		animation: fade-in 0.4s ease both;
	}

	.nav-item:first-child {
		border-top: 1px solid var(--color-border);
	}

	.nav-item:hover {
		color: var(--color-text-bright);
		padding-left: 0.5rem;
	}

	.nav-item.active {
		color: var(--color-text-bright);
	}

	.nav-index {
		font-size: 0.55rem;
		color: var(--color-text-muted);
		font-weight: 300;
		min-width: 1.5rem;
	}

	.nav-item:hover .nav-index,
	.nav-item.active .nav-index {
		color: var(--color-highlight);
	}

	.nav-text {
		font-size: 1.1rem;
		font-weight: 600;
		letter-spacing: 0.12em;
	}

	.nav-dot {
		width: 6px;
		height: 6px;
		border-radius: 50%;
		background: var(--color-highlight);
		margin-left: auto;
		animation: pulse-slow 2s ease-in-out infinite;
	}
</style>
