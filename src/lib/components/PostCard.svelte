<script lang="ts">
	interface Props {
		title: string;
		date: string;
		tag: string;
		excerpt: string;
		index?: number;
	}

	let { title, date, tag, excerpt, index = 0 }: Props = $props();
</script>

<article class="post" style="animation-delay: {0.2 + index * 0.08}s">
	<div class="post-top">
		<span class="post-num">{String(index + 1).padStart(2, '0')}</span>
		<span class="post-tag">{tag}</span>
		<span class="post-date">{date}</span>
	</div>
	<h3 class="post-title">{title}</h3>
	<p class="post-excerpt">{excerpt}</p>
</article>

<style>
	.post {
		padding: 1.5rem 0;
		border-bottom: 1px solid var(--color-dark-surface);
		cursor: pointer;
		position: relative;
		animation: fade-in 0.5s ease both;
		transition: all 0.2s ease;
	}

	.post:first-child {
		border-top: 1px solid var(--color-dark-surface);
	}

	.post:hover {
		padding-left: 1rem;
	}

	.post:hover .post-title {
		color: #fff;
	}

	.post:hover .post-num {
		color: var(--color-highlight);
	}

	.post-top {
		display: flex;
		align-items: center;
		gap: 1rem;
		margin-bottom: 0.6rem;
	}

	.post-num {
		font-size: 0.6rem;
		color: var(--color-dark-text-dim);
		font-weight: 300;
		transition: color 0.2s;
	}

	.post-tag {
		font-size: 0.55rem;
		letter-spacing: 0.15em;
		text-transform: uppercase;
		color: var(--color-highlight);
		font-weight: 600;
	}

	.post-date {
		font-size: 0.55rem;
		color: var(--color-dark-text-dim);
		margin-left: auto;
	}

	.post-title {
		font-size: 1.05rem;
		font-weight: 600;
		color: var(--color-dark-text);
		line-height: 1.4;
		letter-spacing: -0.01em;
		margin-bottom: 0.4rem;
		transition: color 0.2s;
	}

	.post-excerpt {
		font-size: 0.7rem;
		color: var(--color-dark-text-dim);
		line-height: 1.7;
		display: -webkit-box;
		-webkit-line-clamp: 2;
		-webkit-box-orient: vertical;
		overflow: hidden;
	}
</style>
