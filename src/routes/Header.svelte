<script>
	import logo from '$lib/products/nurpur.png';
	import MenuOverlay from './Menu.svelte';
	import './styles.css';
	import 'iconify-icon';

	import { onMount } from 'svelte';

	onMount(() => {
		const video = document.querySelector('#backgroundVideo');

		function checkScroll() {
			if (window.pageYOffset > 10) {
				video.style.opacity = '0';
				setTimeout(() => {
					if (video.style.opacity === '0') {
						video.pause();
					}
				}, 1000);
			} else {
				video.style.opacity = '0.3';
				video.play();
			}
		}

		checkScroll();
		window.addEventListener('scroll', checkScroll);

		return () => {
			window.removeEventListener('scroll', checkScroll);
		};
	});

	let isLightMode = false;
	function toggle() {
		isLightMode = !isLightMode;
		const icon = document.querySelector('.mode-icon');
		if (isLightMode) {
			icon.setAttribute('icon', 'mdi-lightbulb-on-outline');
			icon.style.color = 'black';
		} else {
			icon.setAttribute('icon', 'mdi-lightbulb-outline');
			icon.style.color = 'white';
		}
		document.body.classList.toggle('lightMode');
	}

	let isMenuOpen = false;

	const toggleMenu = () => {
		isMenuOpen = !isMenuOpen;
	};
</script>

<video id="backgroundVideo" autoplay loop muted playsinline preload="auto">
	<source src="src/lib/video.mp4" type="video/mp4" />
</video>

<header>
	<a data-sveltekit-prefetch data-sveltekit-noscroll href="/">
		<div class="logo">
			<div class="white-circle" />
		</div>
	</a>

	<div class="mode" on:click={toggle} on:keypress={toggle}>
		<iconify-icon class="mode-icon" icon="mdi-lightbulb-outline" style="color: white;" />
	</div>

	<button
		class="hamburger hamburger--arrow"
		type="button"
		on:click={toggleMenu}
		class:is-active={isMenuOpen}
	>
		<span class="hamburger-box">
			<span class="hamburger-inner" />
		</span>
	</button>
</header>

<MenuOverlay isOpen={isMenuOpen} closeMenu={() => (isMenuOpen = false)} />

<slot />

<style>
	#backgroundVideo {
		position: fixed;
		right: 0;
		bottom: 0;
		min-width: 100%;
		min-height: 100%;
		z-index: 0;
		transition: opacity 0.8s ease;
		opacity: 0.5;
	}

	.white-circle {
		width: 50px;
		height: 50px;
		background-color: white;
		border-radius: 50%;
		position: absolute;
		left: 50%;
		transform: translate(-50%, -50%);
		z-index: 1;
	}

	button {
		transform: scale(0.8);

		padding-left: 20px;
		padding-right: 20px;
		padding-top: 20px;
		padding-bottom: 20px;

		position: absolute;
		top: 38px;
		right: 80px;
		cursor: pointer;

		z-index: 1000;
	}

	.mode {
		color: var(--darkColor);
		padding-left: 10px;
		padding-right: 10px;
		padding-top: 10px;
		padding-bottom: 10px;

		position: absolute;
		top: 55px;
		left: 80px;
		cursor: pointer;
		transition: 0.3s ease;
		transform: scale(1.7);
	}

	a {
		text-decoration: none !important;
	}

	header {
		display: flex;
		justify-content: center;
		align-items: center;
		height: 100px;
		margin-top: 10px;
		margin-bottom: 30px;
	}

	@media (max-width: 1000px) {
		button {
			display: block;
		}

		button {
			top: 38px;
			right: 15px;
			transform: scale(0.67);
		}

		* {
			font-weight: 400 !important;
		}

		.logo {
			transform: scale(0.75) !important;
		}

		header {
			margin-bottom: 10px;
		}

		.mode {
			top: 55px;
			left: 30px;
			transform: scale(1.6);
		}
	}
</style>
