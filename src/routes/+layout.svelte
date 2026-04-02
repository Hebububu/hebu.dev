<script lang="ts">
	import './layout.css';
	import favicon from '$lib/assets/favicon.svg';
	import { onMount } from 'svelte';

	let { children } = $props();
	let ready = $state(false);

	onMount(() => {
		const tag = document.createElement('script');
		tag.src = 'https://www.youtube.com/iframe_api';
		document.head.appendChild(tag);

		(window as any).onYouTubeIframeAPIReady = () => {
			const player = new (window as any).YT.Player('yt-bg-player', {
				videoId: 'EEk4JGzqoFg',
				playerVars: {
					autoplay: 1,
					mute: 1,
					loop: 1,
					playlist: 'EEk4JGzqoFg',
					controls: 0,
					showinfo: 0,
					modestbranding: 1,
					rel: 0,
					disablekb: 1,
					playsinline: 1,
					origin: window.location.origin
				},
				events: {
					onReady: (e: any) => {
						e.target.playVideo();
						(window as any).__ytPlayer = player;
					},
					onStateChange: (e: any) => {
						if (e.data === (window as any).YT.PlayerState.PLAYING && !ready) {
							ready = true;
							// Unmute with low volume on first user interaction
							const unmute = () => {
								player.unMute();
								player.setVolume(2);
								document.removeEventListener('click', unmute);
								document.removeEventListener('keydown', unmute);
							};
							document.addEventListener('click', unmute);
							document.addEventListener('keydown', unmute);
						}
					}
				}
			});
		};
	});
</script>

<svelte:head>
	<link rel="icon" href={favicon} />
	<title>hebu.dev</title>
</svelte:head>

<div class="yt-bg">
	<div id="yt-bg-player"></div>
	<div class="yt-blur-overlay"></div>
	<div class="yt-cover" class:ready></div>
</div>

<div class="void">
	<div class="viewport">
		<div class="noise"></div>
		{@render children()}
	</div>
</div>

<style>
	.yt-bg {
		position: fixed;
		inset: 0;
		z-index: 0;
		overflow: hidden;
		background: #f7f7f7;
	}
	.yt-bg :global(iframe) {
		position: absolute;
		top: 50%;
		left: 50%;
		width: 100vw;
		height: 56.25vw; /* 16:9 */
		min-height: 100vh;
		min-width: 177.78vh; /* 16:9 */
		transform: translate(-50%, -50%);
		pointer-events: none;
		filter: blur(7px);
	}

	.yt-blur-overlay {
		position: absolute;
		inset: 0;
		background: rgba(247, 247, 247, 0.3);
		pointer-events: all;
	}
	.yt-cover {
		position: absolute;
		inset: 0;
		background: #f7f7f7;
		transition: opacity 2s ease 1s;
	}
	.yt-cover.ready {
		opacity: 0;
		pointer-events: none;
	}

	.void {
		position: relative;
		width: 100%;
		height: calc(100dvh - 20px);
		z-index: 1;
	}

	.viewport {
		position: relative;
		width: 80%;
		max-width: 1400px;
		height: 100%;
		margin: 0 auto;
		border-radius: 16px;
		overflow: hidden;
		box-shadow:
			0 0 0 1px rgba(255, 255, 255, 0.25),
			0 8px 80px -12px rgba(93, 78, 117, 0.15);
	}

	@media (max-width: 900px) {
		.viewport {
			width: 100%;
			border-radius: 0;
		}
	}

	.noise {
		position: absolute;
		inset: 0;
		background-image: url("data:image/svg+xml,%3Csvg viewBox='0 0 512 512' xmlns='http://www.w3.org/2000/svg'%3E%3Cfilter id='n'%3E%3CfeTurbulence type='fractalNoise' baseFrequency='0.75' numOctaves='4' stitchTiles='stitch'/%3E%3C/filter%3E%3Crect width='100%25' height='100%25' filter='url(%23n)' opacity='0.045'/%3E%3C/svg%3E");
		pointer-events: none;
		z-index: 50;
		mix-blend-mode: multiply;
	}
</style>
