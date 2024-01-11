<script>
	import { slide } from 'svelte/transition';
	import companyTree from '$lib/company.json';
	export let isOpen;
	export let title;

	let owner = '';
	let description = '';

	$: if (companyTree[title]) {
		owner = companyTree[title].owner;
		description = companyTree[title].description;
	}

	function close() {
		isOpen = false;
	}
</script>

{#if isOpen}
	<div class="overlay" on:click={close} transition:slide={{ delay: 0, duration: 250, axis: 'y' }}>
		<div class="overlay-content">
			<h2>{title}</h2>
			{#if companyTree[title]}
				<h3>> {owner}</h3>
				<p>{description}</p>
			{:else}
				<p>Company information not available.</p>
			{/if}
		</div>
	</div>
{/if}

<style>
	:global(body) .overlay {
		position: fixed;
		top: 0;
		left: 0;
		width: 100%;
		height: 100%;
		background-color: rgb(16, 16, 16);
		z-index: 9999;

		display: flex;
		align-items: center;
		justify-content: center;
	}

	:global(body) .overlay-content {
		padding: 50px;
		border-radius: 30px;

		background: rgba(42, 42, 42, 0.586);
		width: auto;
		max-width: 500px;
		text-align: left;
		color: var(--lightColor);
	}

	.overlay-content h2 {
		font-size: 1.9rem;
		margin-bottom: 0.5rem;
		font-weight: 500;
	}

	.overlay-content h3 {
		font-size: 1.3rem;
		margin-bottom: 1rem;
		font-weight: 400;
	}

	.overlay-content p {
		font-size: 1rem;
		line-height: 2;
	}

	/* Light Mode */

	:global(body.lightMode) .overlay {
		color: var(--darkColor);
		background-color: rgb(206, 206, 206);
	}

	:global(body.lightMode) .overlay-content {
		background: rgb(221, 221, 221);
		color: var(--darkColor);
	}

	@media (max-width: 450px) {
		.overlay-content {
			transform: scale(0.8);
		}
	}
</style>
