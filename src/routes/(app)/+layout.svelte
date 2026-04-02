<script lang="ts">
	import {
		GithubLogo,
		DiscordLogo,
		EnvelopeSimple,
		TwitterLogo,
		SpeakerSimpleHigh,
		SpeakerSimpleSlash
	} from 'phosphor-svelte';
	import { onMount } from 'svelte';
	import { page } from '$app/stores';

	let { children } = $props();

	let mounted = $state(false);
	let time = $state('');
	let isMuted = $state(true);

	function getVideo(): HTMLVideoElement | null {
		return document.querySelector('.video-bg video');
	}

	function toggleMute() {
		const video = getVideo();
		if (!video) return;
		if (video.muted) {
			video.muted = false;
			video.volume = 0.02;
		} else {
			video.muted = true;
		}
		isMuted = video.muted;
	}

	// Contribution grid from GitHub data (fetched client-side)
	let contributions: number[][] = $state([]);

	async function fetchContributions() {
		try {
			const res = await fetch('https://github-contributions-api.jogruber.de/v4/Hebububu?y=last');
			const json = await res.json();
			const days: { date: string; level: number }[] = json.contributions ?? [];
			// Group into weeks of 7 days
			const weeks: number[][] = [];
			for (let i = 0; i < days.length; i += 7) {
				weeks.push(days.slice(i, i + 7).map((d) => d.level));
			}
			contributions = weeks.length > 16 ? weeks.slice(-16) : weeks;
		} catch {
			contributions = [];
		}
	}

	const links = [
		{ label: 'github.com/Hebububu', url: 'https://github.com/Hebububu', icon: GithubLogo },
		{ label: 'hebu', url: 'https://discord.com/users/hebu', icon: DiscordLogo },
		{ label: '2rwin_rommel@naver.com', url: 'mailto:2rwin_rommel@naver.com', icon: EnvelopeSimple },
		{ label: '@Hebu_VRC', url: 'https://x.com/Hebu_VRC', icon: TwitterLogo }
	];

	const navItems = [
		{ id: 'home', label: 'Home', href: '/' },
		{ id: 'works', label: 'Works', href: '/works' },
		{ id: 'thoughts', label: 'Thoughts', href: '/thoughts' },
		{ id: 'archive', label: 'Archive', href: '/archive' },
		{ id: 'portfolio', label: 'Portfolio', href: '/portfolio' }
	];

	function isActive(href: string, pathname: string) {
		if (href === '/') return pathname === '/';
		return pathname.startsWith(href);
	}

	function updateTime() {
		const now = new Date();
		time = now.toLocaleTimeString('en-US', {
			hour12: false,
			hour: '2-digit',
			minute: '2-digit'
		});
	}

	onMount(() => {
		mounted = true;
		updateTime();
		// Only shows HH:MM — sync to next minute boundary, then update every 60s
		const msUntilNextMinute = (60 - new Date().getSeconds()) * 1000;
		let interval: ReturnType<typeof setInterval>;
		const minuteTimeout = setTimeout(() => {
			updateTime();
			interval = setInterval(updateTime, 60000);
		}, msUntilNextMinute);
		fetchContributions();
		return () => {
			clearTimeout(minuteTimeout);
			if (interval) clearInterval(interval);
		};
	});
</script>

<div class="page" class:mounted>
	<div class="panels-wrap">
	<!-- === LEFT PANEL (30%) === -->
	<div class="panel-left">
		<!-- Mute toggle -->
		<div class="mute-bar">
			<button class="mute-toggle" onclick={toggleMute} aria-label={isMuted ? 'Unmute' : 'Mute'}>
				{#if isMuted}
					<SpeakerSimpleSlash size={14} weight="regular" />
				{:else}
					<SpeakerSimpleHigh size={14} weight="regular" />
				{/if}
			</button>
		</div>

		<!-- Identity card -->
		<div class="identity-card">
			<div class="name-block">
				<h1 class="name">Hebu</h1>
				<p class="tagline">digital wanderer</p>
			</div>
			<div class="contrib-tile">
				<div class="contrib-grid">
					{#each contributions as week, wi}
						<div class="contrib-col">
							{#each week as level, di}
								<div
									class="contrib-cell"
									data-level={level}
									style="animation-delay: {(wi * 7 + di) * 0.008}s"
								></div>
							{/each}
						</div>
					{/each}
				</div>
			</div>

			<div class="links-section">
				{#each links as link, i}
					<a
						href={link.url}
						class="link-item"
						target="_blank"
						rel="noopener noreferrer"
						style="animation-delay: {0.3 + i * 0.06}s"
					>
						<link.icon size={18} weight="regular" />
						<span>{link.label}</span>
					</a>
				{/each}
			</div>
		</div>

		<!-- Navbar -->
		<nav class="nav-section">
			{#each navItems as item, i}
				<a
					class="nav-btn"
					class:active={isActive(item.href, $page.url.pathname)}
					href={item.href}
					style="animation-delay: {0.2 + i * 0.06}s"
				>
					<span class="nav-index">{String(i + 1).padStart(2, '0')}</span>
					<span class="nav-text">{item.label}</span>
					{#if isActive(item.href, $page.url.pathname)}
						<span class="nav-dot"></span>
					{/if}
				</a>
			{/each}
		</nav>

		<!-- Status -->
		<div class="status-area">
			<div class="status-block">
				<span class="status-dot"></span>
				<span class="status-text">do not disturb</span>
				<span class="status-sep">/</span>
				<span class="status-text dim">hebu nice day</span>
			</div>
			<span class="clock">{time}</span>
		</div>
	</div>

	<!-- === RIGHT PANEL (70%) === -->
	<div class="panel-right">
		<div class="content-inner">
			{@render children()}
		</div>
	</div>
	</div>

	<!-- Marquee -->
	<div class="marquee-rail">
		<div class="marquee-track">
			<span>{'HEBU.DEV \u00b7 '.repeat(20)}</span>
				<span>{'HEBU.DEV \u00b7 '.repeat(20)}</span>
		</div>
	</div>
</div>

<style>
	.page {
		position: relative;
		width: 100%;
		height: 100%;
		display: flex;
		flex-direction: column;
		opacity: 0;
		transition: opacity 0.7s ease;
		overflow: hidden;
	}
	.page.mounted { opacity: 1; }

	.panels-wrap {
		display: flex;
		flex: 1 1 0;
		min-height: 0;
		overflow: hidden;
	}

	/* === LEFT PANEL === */
	.panel-left {
		width: 30%;
		min-width: 280px;
		height: 100%;
		display: flex;
		flex-direction: column;
		padding: 2.5rem 2rem;
		background: rgba(247, 247, 247, 0.92);
		z-index: 2;
		overflow-y: auto;
	}

	/* === RIGHT PANEL — liquid glass === */
	.panel-right {
		width: 70%;
		height: 100%;
		position: relative;
		background: rgba(93, 78, 117, 0.35);
		border-left: 1px solid rgba(93, 78, 117, 0.15);
		box-shadow:
			inset 1px 0 0 rgba(255, 255, 255, 0.18),
			inset 0 1px 0 rgba(255, 255, 255, 0.12);
		color: #0a0a0e;
		z-index: 1;
		overflow-y: auto;
	}

	.content-inner {
		display: flex;
		flex-direction: column;
		justify-content: center;
		min-height: 100%;
		padding: 3rem 3.5rem;
	}

	/* === MUTE TOGGLE === */
	.mute-bar {
		margin-bottom: 1.25rem;
	}
	.mute-toggle {
		display: flex;
		align-items: center;
		justify-content: center;
		width: 22px;
		height: 22px;
		border: none;
		background: none;
		color: var(--color-text-muted);
		cursor: pointer;
		padding: 0;
		transition: color 0.2s ease;
	}
	.mute-toggle:hover {
		color: var(--color-highlight);
	}

	/* === IDENTITY CARD === */
	.identity-card {
		display: flex;
		flex-direction: column;
		gap: 0.25rem;
		margin-bottom: 2.5rem;
	}

	.name-block {
		display: flex;
		flex-direction: column;
		gap: 0.15rem;
	}
	.name {
		font-size: 4rem;
		font-weight: 700;
		color: var(--color-text-bright);
		letter-spacing: -0.05em;
		line-height: 0.9;
	}
	.tagline {
		font-size: 0.85rem;
		color: var(--color-text-dim);
		font-style: italic;
		padding-left: 0.15rem;
	}

	.contrib-tile {
		padding: 0.6rem 0.7rem;
		border: 1px solid var(--color-dark-border);
		border-radius: 6px;
		background: rgba(178, 178, 178, 0.05);
		margin-bottom: 1rem;
		overflow: hidden;
	}
	.contrib-grid {
		display: flex;
		gap: 3px;
		width: 100%;
	}
	.contrib-col {
		display: flex;
		flex-direction: column;
		gap: 3px;
		flex: 1;
	}
	.contrib-cell {
		width: 100%;
		aspect-ratio: 1;
		border-radius: 2px;
		background: #c8c8c8;
		opacity: 0;
		transform: scale(0);
		animation: cell-pop 0.3s ease forwards;
	}
	.contrib-cell[data-level="1"] {
		--cell-color: #a99bbe;
	}
	.contrib-cell[data-level="2"] {
		--cell-color: #7e6d9a;
	}
	.contrib-cell[data-level="3"] {
		--cell-color: #5d4e75;
	}
	.contrib-cell[data-level="4"] {
		--cell-color: #3d3250;
	}

	.contrib-cell[data-level="0"] {
		animation: cell-pop-zero 0.3s ease forwards;
	}
	@keyframes cell-pop-zero {
		0% { opacity: 0; transform: scale(0); }
		60% { opacity: 1; transform: scale(1.2); }
		100% { opacity: 1; transform: scale(1); background: #c8c8c8; }
	}
	@keyframes cell-pop {
		0% { opacity: 0; transform: scale(0); }
		60% { opacity: 1; transform: scale(1.2); }
		100% { opacity: 1; transform: scale(1); background: var(--cell-color); }
	}

	.links-section {
		display: flex;
		flex-direction: column;
		gap: 0.7rem;
	}
	.link-item {
		display: flex;
		align-items: center;
		gap: 0.75rem;
		font-family: var(--font-mono);
		font-size: 0.9rem;
		color: var(--color-text-dim);
		text-decoration: none;
		transition: color 0.2s ease, transform 0.2s ease;
		animation: fade-in 0.4s ease both;
	}
	.link-item:hover {
		color: var(--color-text-bright);
		transform: translateX(6px);
	}
	.link-item :global(svg) {
		flex-shrink: 0;
		color: var(--color-text-muted);
		transition: color 0.2s;
	}
	.link-item:hover :global(svg) {
		color: var(--color-highlight);
	}

	/* === NAV SECTION === */
	.nav-section {
		display: flex;
		flex-direction: column;
		margin-bottom: auto;
	}
	.nav-btn {
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
		text-decoration: none;
		position: relative;
		overflow: hidden;
		animation: fade-in 0.4s ease both;
		transition: color 0.35s cubic-bezier(0.25, 0.46, 0.45, 0.94),
			padding-left 0.35s cubic-bezier(0.25, 0.46, 0.45, 0.94),
			background-color 0.35s cubic-bezier(0.25, 0.46, 0.45, 0.94);
	}
	.nav-btn:first-child {
		border-top: 1px solid var(--color-border);
	}
	.nav-btn:hover {
		color: var(--color-text-bright);
		padding-left: 0.75rem;
		background: color-mix(in srgb, var(--color-highlight) 6%, transparent);
	}
	.nav-btn.active {
		color: var(--color-text-bright);
		padding-left: 0.75rem;
		background: color-mix(in srgb, var(--color-highlight) 8%, transparent);
	}

	.nav-btn.active::before {
		content: '';
		position: absolute;
		left: 0;
		top: 0;
		bottom: 0;
		width: 3px;
		background: var(--color-highlight);
		animation: slide-in-left 0.35s cubic-bezier(0.25, 0.46, 0.45, 0.94) both;
	}

	.nav-index {
		font-size: 0.7rem;
		color: var(--color-text-muted);
		font-weight: 300;
		min-width: 1.5rem;
		transition: color 0.2s;
	}
	.nav-btn:hover .nav-index,
	.nav-btn.active .nav-index {
		color: var(--color-highlight);
	}
	.nav-text {
		font-size: 1.3rem;
		font-weight: 600;
		letter-spacing: 0.08em;
		transition: transform 0.35s cubic-bezier(0.25, 0.46, 0.45, 0.94);
	}
	.nav-btn.active .nav-text {
		animation: text-slide-right 0.35s cubic-bezier(0.25, 0.46, 0.45, 0.94) both;
	}
	.nav-dot {
		width: 7px;
		height: 7px;
		border-radius: 50%;
		background: var(--color-highlight);
		margin-left: auto;
		animation: pulse-slow 2s ease-in-out infinite;
	}

	/* === STATUS AREA === */
	.status-area {
		display: flex;
		justify-content: space-between;
		align-items: flex-end;
		padding-top: 1.5rem;
	}
	.status-block {
		display: flex;
		align-items: center;
		gap: 0.5rem;
		font-size: 0.8rem;
	}
	.status-dot {
		width: 7px;
		height: 7px;
		border-radius: 50%;
		background: var(--color-highlight);
		animation: pulse-slow 2s ease-in-out infinite;
	}
	.status-text {
		color: var(--color-text-dim);
	}
	.status-text.dim {
		color: var(--color-text-muted);
	}
	.status-sep {
		color: var(--color-border);
	}
	.clock {
		font-size: 1.8rem;
		font-weight: 700;
		color: var(--color-text-bright);
		letter-spacing: -0.04em;
		line-height: 1;
		animation: flicker 8s infinite;
	}

	/* === MARQUEE === */
	.marquee-rail {
		flex-shrink: 0;
		overflow: hidden;
		padding: 0.55rem 0;
		border-top: 1px solid color-mix(in srgb, var(--color-border) 50%, transparent);
		background: #474747;
		z-index: 3;
	}
	.marquee-track {
		display: flex;
		white-space: nowrap;
		animation: marquee 40s linear infinite;
		will-change: transform;
	}
	.marquee-track span {
		font-size: 0.55rem;
		letter-spacing: 0.35em;
		color: var(--color-text-muted);
		font-weight: 400;
		line-height: 1.6;
	}

	/* === ANIMATIONS === */
	@keyframes slide-in-left {
		from { transform: scaleY(0); opacity: 0; }
		to { transform: scaleY(1); opacity: 1; }
	}
	@keyframes text-slide-right {
		from { transform: translateX(-8px); opacity: 0.5; }
		to { transform: translateX(0); opacity: 1; }
	}

	/* === RESPONSIVE === */
	@media (max-width: 900px) {
		.panels-wrap {
			flex-direction: column;
			overflow-y: auto;
		}
		.panel-left {
			width: 100%;
			min-width: 0;
			height: auto;
			flex-shrink: 0;
			padding: 1.5rem 1rem;
			overflow-y: visible;
		}
		.panel-right {
			width: 100%;
			height: auto;
			flex-shrink: 0;
			border-left: none;
			border-top: 1px solid color-mix(in srgb, var(--color-sub) 25%, transparent);
			overflow-y: visible;
		}
		.content-inner {
			padding: 1.5rem 1rem;
			min-height: auto;
		}
		.name { font-size: 2.8rem; }
		.clock { font-size: 1.4rem; }
		.nav-btn {
			padding: 0.6rem 0;
			gap: 0.6rem;
		}
		.nav-text {
			font-size: 1rem;
			letter-spacing: 0.05em;
		}
		.nav-index {
			font-size: 0.6rem;
		}
		.nav-dot {
			width: 5px;
			height: 5px;
		}
		.identity-card {
			margin-bottom: 1.5rem;
		}
	}
</style>
