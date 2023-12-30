<script>
	let imageLoaded = false;

	function handleImageLoad() {
		imageLoaded = true;
	}

	function handleImageError() {
		console.error('Image failed to load');
	}
</script>

<svelte:head>
	<title>Why Boycott?</title>
	<meta name="description" content={'Boycott Does Make a Difference.'} />
</svelte:head>

<div class="imageContainer">
	{#if !imageLoaded}
		<div class="bigContainer">LOADING IMAGE</div>
	{/if}

	<img
		class="tiltMain"
		src={'https://www.placehold.co/800x400'}
		alt={'Bird flying with water in its beak'}
		on:load={handleImageLoad}
		on:error={handleImageError}
		style={imageLoaded ? 'display:block;' : 'display:none;'}
	/>
</div>

<div class="text-container">
	<div class="text">
		<p>
			When Prophet Ibrahim (Hazrat Ibrahim AS) was thrown into the fire by the order of King Nimrod.
			A tiny bird, an Ababeel, perched in a tree witnessed the injustice and without hesitation,
			sprang into action.
		</p>

		<p>
			The little bird collected water from a nearby river in its beak and returned to the scene,
			releasing the drops over the raging fire.
		</p>
		<p>
			Several larger birds nearby watched and mocked the little bird, asking, "Why are you carrying
			water?"
		</p>
		<p>
			The Ababeel replied, "I am trying to put out the fire around Prophet Ibrahim (PBUH)." The
			larger birds continued laughing, saying, "How can you extinguish such a huge fire with so
			little water? It's pointless and useless."
		</p>
		<p>
			But the little bird had a profound response, "I am certain Allah (SWT) will not ask me whether
			I managed to put out the fire or not. But Allah (SWT) will ask, being in the moment, what I
			did to stop the fire, and I'll be able to say that I tried to do the best I could."
		</p>
	</div>
</div>
```

<style>
	.bigContainer {
		display: grid;
		place-items: center;
		height: 500px;
	}

	.tiltMain {
		display: none;
	}

	.text-container {
		display: grid;
		place-items: center;
		width: 100%;
	}

	.text {
		width: 70%;
	}

	p {
		margin-bottom: 50px;
		font-weight: 300;
	}
</style>
