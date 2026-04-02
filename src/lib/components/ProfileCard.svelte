<script lang="ts">
	import { onMount } from 'svelte';

	let time = $state('');
	let mounted = $state(false);

	const links = [
		{ label: 'GITHUB', url: 'https://github.com/hebu' },
		{ label: 'DISCORD', url: '#' },
		{ label: 'EMAIL', url: 'mailto:hello@hebu.dev' },
		{ label: 'BLOG', url: '#' }
	];

	const status = {
		state: 'awake',
		doing: 'building things at 3am',
		mood: 'melancholic but productive'
	};

	function updateTime() {
		const now = new Date();
		time = now.toLocaleTimeString('en-US', {
			hour12: false,
			hour: '2-digit',
			minute: '2-digit',
			second: '2-digit'
		});
	}

	onMount(() => {
		mounted = true;
		updateTime();
		const interval = setInterval(updateTime, 1000);
		return () => clearInterval(interval);
	});
</script>

<aside class="card" class:mounted>
	<!-- big name -->
	<div class="hero-name">
		<span class="hero-h">H</span>
		<div class="hero-rest">
			<span class="name-text">hebu</span>
			<span class="tagline">digital wanderer</span>
		</div>
	</div>

	<!-- status block — dark inversion -->
	<div class="status-block">
		<div class="status-header">
			<span class="status-label">STATUS</span>
			<span class="status-time">{time}</span>
		</div>
		<div class="status-rows">
			<div class="status-row">
				<span class="key">state</span>
				<span class="val">{status.state}</span>
			</div>
			<div class="status-row">
				<span class="key">doing</span>
				<span class="val">{status.doing}</span>
			</div>
			<div class="status-row">
				<span class="key">mood</span>
				<span class="val highlight">{status.mood}</span>
			</div>
		</div>
	</div>

	<!-- links — horizontal pills -->
	<nav class="links">
		{#each links as link, i}
			<a
				href={link.url}
				class="link-pill"
				style="animation-delay: {0.4 + i * 0.06}s"
				target="_blank"
				rel="noopener noreferrer"
			>
				{link.label}
			</a>
		{/each}
	</nav>

	<!-- bottom whisper -->
	<div class="whisper">
		<span>quietly existing on the internet</span>
	</div>
</aside>

<style>
	.card {
		display: flex;
		flex-direction: column;
		gap: 2rem;
		padding: 2.5rem 2rem;
		height: 100%;
		opacity: 0;
		transition: opacity 0.8s ease;
	}
	.card.mounted {
		opacity: 1;
	}

	/* hero name */
	.hero-name {
		display: flex;
		align-items: flex-start;
		gap: 0.25rem;
	}
	.hero-h {
		font-size: 6rem;
		font-weight: 700;
		line-height: 0.8;
		color: var(--color-text-bright);
		letter-spacing: -0.06em;
		position: relative;
	}
	.hero-h::after {
		content: '';
		position: absolute;
		bottom: 4px;
		left: 0;
		width: 100%;
		height: 3px;
		background: var(--color-highlight);
	}
	.hero-rest {
		display: flex;
		flex-direction: column;
		padding-top: 0.75rem;
		gap: 0.2rem;
	}
	.name-text {
		font-size: 1.1rem;
		font-weight: 600;
		color: var(--color-text-bright);
		letter-spacing: 0.15em;
		text-transform: uppercase;
	}
	.tagline {
		font-size: 0.65rem;
		color: var(--color-text-dim);
		font-style: italic;
	}

	/* status — dark card */
	.status-block {
		background: var(--color-dark);
		color: var(--color-dark-text);
		padding: 1.25rem;
		border-radius: 10px;
		border: 1px solid color-mix(in srgb, var(--color-sub) 20%, transparent);
	}
	.status-header {
		display: flex;
		justify-content: space-between;
		align-items: center;
		margin-bottom: 1rem;
		padding-bottom: 0.6rem;
		border-bottom: 1px solid var(--color-dark-surface);
	}
	.status-label {
		font-size: 0.6rem;
		letter-spacing: 0.2em;
		color: var(--color-sub);
		font-weight: 600;
	}
	.status-time {
		font-size: 0.65rem;
		color: var(--color-dark-text-dim);
		animation: flicker 6s infinite;
	}
	.status-rows {
		display: flex;
		flex-direction: column;
		gap: 0.5rem;
	}
	.status-row {
		display: flex;
		gap: 1rem;
		font-size: 0.75rem;
	}
	.key {
		color: var(--color-dark-text-dim);
		min-width: 3rem;
		font-size: 0.65rem;
	}
	.val {
		color: var(--color-dark-text);
	}
	.val.highlight {
		color: var(--color-highlight);
	}

	/* links */
	.links {
		display: flex;
		flex-wrap: wrap;
		gap: 0.5rem;
	}
	.link-pill {
		font-family: var(--font-mono);
		font-size: 0.65rem;
		font-weight: 600;
		letter-spacing: 0.12em;
		color: var(--color-text-bright);
		text-decoration: none;
		padding: 0.5rem 1.1rem;
		border: 1.5px solid var(--color-text-bright);
		border-radius: 100px;
		transition: all 0.25s ease;
		animation: fade-in 0.4s ease both;
	}
	.link-pill:hover {
		background: var(--color-text-bright);
		color: var(--color-bg);
		border-color: var(--color-text-bright);
		transform: translateY(-2px);
		box-shadow: 0 4px 20px color-mix(in srgb, var(--color-sub) 30%, transparent);
	}

	/* whisper */
	.whisper {
		margin-top: auto;
		font-size: 0.55rem;
		color: var(--color-text-muted);
		font-style: italic;
		letter-spacing: 0.03em;
	}
</style>
