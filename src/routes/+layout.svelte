<script lang="ts">
	import './layout.css';
	import favicon from '$lib/assets/favicon.svg';
	import { onMount } from 'svelte';

	let { children } = $props();
	let videoEl: HTMLVideoElement;

	onMount(() => {
		// Unmute with low volume on first user interaction
		const unmute = () => {
			if (videoEl) {
				videoEl.muted = false;
				videoEl.volume = 0.02;
			}
			document.removeEventListener('click', unmute);
			document.removeEventListener('keydown', unmute);
		};
		document.addEventListener('click', unmute);
		document.addEventListener('keydown', unmute);
		return () => {
			document.removeEventListener('click', unmute);
			document.removeEventListener('keydown', unmute);
		};
	});
</script>

<svelte:head>
	<link rel="icon" href={favicon} />
	<link rel="preconnect" href="https://fonts.googleapis.com" />
	<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin="anonymous" />
	<link
		rel="stylesheet"
		href="https://fonts.googleapis.com/css2?family=JetBrains+Mono:wght@400;600;700&display=swap"
	/>
	<title>hebu.dev</title>
</svelte:head>

<div class="video-bg">
	<video bind:this={videoEl} autoplay loop muted playsinline>
		<source src="/bg.mp4" type="video/mp4" />
	</video>
	<div class="video-overlay"></div>
</div>

<div class="void">
	<div class="viewport">
		<div class="noise"></div>
		{@render children()}
	</div>
</div>

<style>
	.video-bg {
		position: fixed;
		inset: 0;
		z-index: 0;
		overflow: hidden;
		background: #f7f7f7;
	}
	.video-bg video {
		position: absolute;
		top: 50%;
		left: 50%;
		width: 100vw;
		height: 56.25vw; /* 16:9 */
		min-height: 100vh;
		min-width: 177.78vh; /* 16:9 */
		transform: translate(-50%, -50%);
		object-fit: cover;
		pointer-events: none;
		filter: blur(4px);
	}
	.video-overlay {
		position: absolute;
		inset: 0;
		background: rgba(247, 247, 247, 0.3);
		pointer-events: all;
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
		background-image: url("data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAADAAAAAwBAMAAAClLOS0AAAAElBMVEUAAAAAAAAAAAAAAAAAAAAAAADgKxmiAAAABnRSTlMFCA0RFRkEEioAAAD0SURBVDjLhZMxDoQwDATdUlByBK5AygUo+YT/P+V2vWODdkUpRjP2OgGg/NQBTH9sDWD6Y0cA62OvANv//YHrj50BrA+sWQF2fGAFWB/YAqyPPQL4/t8PvB7YA8D6D2sA1h87AZj+2AqgPHMGsP3fH7g+sAeA9WfWAGyPrBlAPuPHQB8B2YF2B84AFj/2BHA+g9rANYfOwGY/tgKoD5zB5j+7w9cD5wBOA+8rgBY/9gjgPUflgG4/tgJwPTHVgDlmTOA7f/+wPXA8wVA+Q+/AOsDOwBY/2ELwPXHTgCmP7YCUM+cAab/+xeuB64AaN8v3IFq/A2xSBIAAAAASUVORK5CYII=");
		background-repeat: repeat;
		opacity: 0.035;
		pointer-events: none;
		z-index: 50;
		mix-blend-mode: multiply;
	}
</style>
