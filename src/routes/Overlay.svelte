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
				<h3>Owned by {owner}</h3>
				<p>{description}</p>
			{:else}
				<p>Company information not available.</p>
			{/if}
		</div>
	</div>
{/if}

<style>
	.overlay {
		position: fixed;
		top: 0;
		left: 0;
		width: 100%;
		height: 100%;
		background-color: rgb(16, 16, 16);
		display: flex;
		justify-content: flex-start;
		align-items: center;
		z-index: 999;
	}

	.overlay-content h2 {
		font-size: 2rem;
		margin-bottom: 0.5rem;
		font-weight: 600;
	}

	.overlay-content h3 {
		font-size: 1.5rem;
		color: #ccc;
		margin-bottom: 1rem;
		font-weight: 400;
	}

	.overlay-content p {
		font-size: 1rem;
		line-height: 1.6;
	}

	.overlay-content {
		padding: 50px;
		border-radius: 5px;

		width: 100%;
		height: 100%;
		z-index: 1000;

		color: var(--lightColor);
		text-align: left;
		max-width: 500px;
	}
</style>
