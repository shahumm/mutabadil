<script>
	import { fade } from 'svelte/transition';
	import '../styles.css';
	import VanillaTilt from 'vanilla-tilt';

	onMount(async () => {
		// Tilt Effect
		VanillaTilt.init(document.querySelectorAll('.imageContainer img'), {
			max: 8,
			speed: 400,
			gyroscope: false
		});
	});

	// STARTS
	import { onMount } from 'svelte';
	let paragraphs = [];
	let spans = [];

	function revealSpans() {
		// // Mobile View
		// if (window.innerWidth < 1000) {
		//   return;
		// }

		// Desktop View
		const windowHeight = window.innerHeight;
		const viewportHeight = windowHeight * 0.8;
		const topThreshold = windowHeight * 0.7;

		for (let i = 0, len = spans.length; i < len; i++) {
			const span = spans[i];
			const parentRect = span.parentElement.getBoundingClientRect();
			const spanRect = span.getBoundingClientRect();

			if (parentRect.top >= viewportHeight) {
				span.style.opacity = 0.1;
			} else {
				const top = spanRect.top - topThreshold;
				const left = spanRect.left;
				const opacityValue = Math.min(Math.max(1 - (top * 0.01 + left * 0.001), 0.1), 1);
				span.style.opacity = opacityValue.toFixed(3);
			}
		}
	}

	onMount(() => {
		paragraphs = [...document.querySelectorAll('p')];

		paragraphs.forEach((paragraph) => {
			let htmlString = '';
			const pArray = paragraph.textContent.split(' ');

			for (let i = 0, len = pArray.length; i < len; i++) {
				htmlString += `<span>${pArray[i]} </span>`;
			}

			paragraph.innerHTML = htmlString;
		});

		spans = [...document.querySelectorAll('span')];

		function handleAnimationFrame() {
			revealSpans();
			requestAnimationFrame(handleAnimationFrame);
		}

		requestAnimationFrame(handleAnimationFrame);
	});
	// ENDS

	export let data;
</script>

<div class="app">
	<main>
		{#key data.url}
			<div in:fade={{ duration: 100, delay: 100 }} out:fade={{ duration: 100 }}>
				<slot />
			</div>
		{/key}
	</main>
</div>

<style>
	:global(.text p) {
		margin-bottom: 4rem;
		font-size: clamp(20px, 3vw, 30px);
		padding: 0 15%;
		line-height: 1.8;
	}

	:global(.imageContainer) {
		display: grid;
		place-items: center;

		margin-bottom: 50px;
		height: auto;
	}

	:global(.imageContainer img) {
		border-radius: 10px;
		box-shadow: 0px 12px 15px 0px rgba(0, 0, 0, 0.1);
		max-width: 700px;
		max-height: 600px;
	}

	:global(.text) {
		margin-bottom: 40vh;
		padding: 0px 100px;
	}

	/* MOBILE VIEW */
	@media (max-width: 1100px) {
		:global(.text) {
			margin-bottom: 30vh;
			padding: 0px;
		}
	}

	@media (max-width: 450px) {
		:global(.imageContainer img) {
			width: 100%;
			border-radius: 0;
		}

		:global(.text) {
			margin-bottom: 30vh;
			padding: 0px;
		}

		:global(.text p) {
			margin-bottom: 3rem;
			padding: 0 8%;
		}
	}
</style>
