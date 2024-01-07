<script>
	import dataTree from '$lib/data.json';
	import Overlay from './Overlay.svelte';
	import { onMount } from 'svelte';
	import { gsap } from 'gsap';

	let switchToLocal;
	let wherePossible;
	let searchBar;

	onMount(() => {
		gsap.from([switchToLocal, wherePossible, searchBar, '.content-container'], {
			y: 80,
			opacity: 0,
			duration: 0.4,
			stagger: 0.2
		});
	});

	// Placeholder Animation
	let placeholders = ['Milk...', 'Dairy...', 'Soaps...', 'Olpers...'];
	let currentPlaceholderIndex = 0;
	let cyclePlaceholder;
	let placeholderText = placeholders[0];

	function startCyclingPlaceholders() {
		cyclePlaceholder = setInterval(() => {
			currentPlaceholderIndex = (currentPlaceholderIndex + 1) % placeholders.length;
			placeholderText = placeholders[currentPlaceholderIndex];
		}, 1000);
	}

	onMount(() => {
		startCyclingPlaceholders();
	});

	function handleFocus() {
		clearInterval(cyclePlaceholder);
		placeholderText = '';
	}

	function handleBlur() {
		if (searchQuery.trim() === '') {
			currentPlaceholderIndex = (currentPlaceholderIndex + 1) % placeholders.length;
			placeholderText = placeholders[currentPlaceholderIndex];

			startCyclingPlaceholders();
		}
	}

	// Overlay Functions
	let isOverlayOpen = false;
	let overlayContent = '';
	let overlayTitle = '';

	function openOverlay(item) {
		overlayContent = item.description;
		overlayTitle = item.company;
		isOverlayOpen = true;
	}

	// Tree Sort
	let searchQuery = '';
	let filteredData = filterData(dataTree[0]);

	function filterData(item) {
		if (typeof item === 'object' && item !== null) {
			if (Array.isArray(item)) {
				return item.map((element) => filterData(element)).filter(Boolean);
			} else {
				if (item.hasOwnProperty('brand')) {
					return {
						brand: item.brand,
						company: item.company || '',
						image: item.image || ''
					};
				}

				const result = Object.fromEntries(
					Object.entries(item).map(([key, value]) => [key, filterData(value)])
				);

				return Object.fromEntries(Object.entries(result).filter(([_, value]) => value !== false));
			}
		} else {
			return false;
		}
	}

	// Reactivity
	$: if (searchQuery) {
		searchData();
	} else {
		filteredData = filterData(dataTree[0]);
	}

	// Tree Search
	function searchData() {
		filteredData = filterData(dataTree[0]);

		if (searchQuery) {
			const searchQueryLower = searchQuery.toLowerCase();
			let newFilteredData = {};

			Object.entries(filteredData).forEach(([key, value]) => {
				let isHeadingMatched = key.toLowerCase().includes(searchQueryLower);
				let subheadingMatches = {};

				if (typeof value === 'object' && value !== null) {
					Object.entries(value).forEach(([subKey, subValue]) => {
						let isSubheadingMatched = subKey.toLowerCase().includes(searchQueryLower);
						let titleMatches = {};

						if (typeof subValue === 'object' && subValue !== null) {
							Object.entries(subValue).forEach(([titleKey, titleValue]) => {
								let isTitleMatched = titleKey.toLowerCase().includes(searchQueryLower);

								let brandMatches = [];
								if (Array.isArray(titleValue)) {
									titleValue = titleValue.filter((brand) => {
										let match =
											brand.brand.toLowerCase().includes(searchQueryLower) ||
											(brand.company && brand.company.toLowerCase() === searchQueryLower); // Check if company name matches
										if (match) brandMatches.push(brand);
										return !match;
									});
								}

								if (brandMatches.length > 0) {
									titleMatches[titleKey] = [...brandMatches, ...titleValue];
								} else if (isTitleMatched || isSubheadingMatched || isHeadingMatched) {
									titleMatches[titleKey] = titleValue;
								}
							});
						}

						if (isSubheadingMatched || isHeadingMatched || Object.keys(titleMatches).length > 0) {
							subheadingMatches[subKey] =
								isSubheadingMatched || isHeadingMatched ? subValue : titleMatches;
						}
					});
				}

				if (isHeadingMatched || Object.keys(subheadingMatches).length > 0) {
					newFilteredData[key] = isHeadingMatched ? value : subheadingMatches;
				}
			});

			filteredData = newFilteredData;
		}
	}

	let isAtTop = true;

	onMount(() => {
		function handleScroll() {
			isAtTop = window.pageYOffset <= 10;
		}

		window.addEventListener('scroll', handleScroll);

		handleScroll();

		return () => {
			window.removeEventListener('scroll', handleScroll);
		};
	});

	function sortBrands(data) {
		const sortedData = {};
		Object.entries(data).forEach(([key, value]) => {
			sortedData[key] = {};

			Object.entries(value).forEach(([subKey, subValue]) => {
				sortedData[key][subKey] = {};

				Object.entries(subValue).forEach(([titleKey, titleValue]) => {
					sortedData[key][subKey][titleKey] = titleValue.sort((a, b) =>
						a.brand.localeCompare(b.brand)
					);
				});
			});
		});
		return sortedData;
	}

	$: if (searchQuery) {
		searchData();
	} else {
		filteredData = sortBrands(filterData(dataTree[0]));
	}

	let showBackToTop = false;

	onMount(() => {
		function handleScroll() {
			showBackToTop = window.pageYOffset > 100;
		}

		window.addEventListener('scroll', handleScroll);

		handleScroll();

		return () => {
			window.removeEventListener('scroll', handleScroll);
		};
	});

	function scrollToTop() {
		window.scrollTo({ top: 0, behavior: 'smooth' });
	}
</script>

<main>
	<button on:click={scrollToTop} class:show={showBackToTop} class="back-to-top">↑</button>

	<div class="hero-section-container">
		<section class="hero-section">
			<div class="hero-container">
				<div class="hero-container">
					<h2 bind:this={switchToLocal} class={isAtTop ? 'h2 addStroke' : ''}>Switch to local,</h2>
					<span bind:this={wherePossible} class={isAtTop ? 'span addStroke' : ''}>
						where possible.</span
					>
				</div>
			</div>
		</section>
	</div>

	<section class="content-section">
		<div class="content-container">
			<div bind:this={searchBar} class="search-container">
				<input
					type="text"
					id="searchInput"
					class="search-bar {isAtTop ? 'searchTopScroll' : 'searchInputScrolled'}"
					bind:value={searchQuery}
					placeholder={placeholderText}
					on:focus={handleFocus}
					on:blur={handleBlur}
				/>
			</div>
			{#if filteredData && Object.keys(filteredData).length > 0}
				{#each Object.entries(filteredData) as [heading, subheadings]}
					<div>
						<h3>{heading}</h3>
						{#each Object.entries(subheadings) as [subheading, titles]}
							<div>
								<h4>{subheading}</h4>
								{#each Object.entries(titles) as [title, items]}
									<div>
										<h5>{title}</h5>
										<div class="cards-container">
											{#each items as item, index}
												<div
													class={isAtTop ? 'card cardTopScroll' : 'card cardScrolled'}
													on:click={() => openOverlay(item)}
												>
													<img
														class="product-image"
														src={item.image}
														alt="{item.brand} product"
														loading="lazy"
													/>
													<p class="brand-name">{item.brand}</p>
													<p class="company-name">{item.company}</p>
												</div>
											{/each}
										</div>
									</div>
								{/each}
							</div>
						{/each}
					</div>
				{/each}
			{:else}
				<p>No matching items found.</p>
			{/if}
		</div>
	</section>
	<Overlay bind:isOpen={isOverlayOpen} content={overlayContent} title={overlayTitle} />
</main>

<style>
	:global(body) .addStroke {
		-webkit-text-fill-color: transparent;
		-webkit-text-stroke-width: 1px;
	}

	.hero-container h2 {
		font-weight: 600;
		font-size: 45px;
		color: var(--lightColor);
		line-height: 1;
	}

	.hero-container span {
		font-weight: 300;
		font-size: 45px;
		color: var(--lightColor);
	}

	:global(body.lightMode) .hero-container h2,
	:global(body.lightMode) .hero-container span {
		color: var(--darkColor);
	}

	.card:hover .product-image {
		transform: scale(0.95);
		transition: 0.25s ease-in-out;
	}

	.back-to-top {
		position: fixed;
		bottom: 20px;
		right: 20px;
		padding: 10px 20px;
		background-color: #333;
		color: white;
		border: none;
		border-radius: 10px;
		cursor: pointer;
		display: none;
		z-index: 1000;
		font-size: 24px;
	}

	.back-to-top.show {
		display: block;
	}

	:global(body) {
		background-color: #1e1e1e !important;
		color: var(--lightColor) !important;
		transition: 0.25s ease-in-out;
	}

	:global(body.lightMode) {
		background-color: #d6d6d6 !important;
		color: var(--darkColor) !important;
	}

	/* At the top of the page - Only outline */
	:global(body) .searchTopScroll {
		box-shadow: none;
		outline: 1.5px solid var(--lightColor);
	}

	:global(body.lightMode) .searchTopScroll {
		box-shadow: none;
		outline: 1.5px solid var(--darkColor);
	}

	:global(body) .searchInputScrolled,
	:global(body) #searchInput:focus {
		box-shadow: inset 8px 8px 8px -1px rgba(0, 0, 0, 0.4),
			inset -8px -8px 8px -1px rgba(56, 56, 56, 0.4);
		outline: none; /* Ensures no outline */
	}

	:global(body.lightMode) .searchInputScrolled,
	:global(body.lightMode) #searchInput:focus {
		box-shadow: inset 10px 10px 10px -1px rgba(0, 0, 0, 0.1),
			inset -10px -10px 10px -1px rgba(255, 255, 255, 0.3);
		outline: none; /* Ensures no outline */
	}

	.content-section {
		overflow-y: auto;
		display: grid;
		place-items: center;
	}

	h3::first-letter,
	h4::first-letter,
	h5::first-letter {
		text-transform: capitalize;
	}

	.content-container h3 {
		font-size: 28px;
		font-weight: 600;
		padding-bottom: 5px;
		z-index: 999;
	}

	.content-container h4 {
		font-size: 22px;
		font-weight: 400;
		padding-bottom: 5px;
		z-index: 999;
	}

	.content-container h5 {
		font-size: 18px;
		font-weight: 300;
		padding-bottom: 20px;
		z-index: 999;
	}

	.hero-section-container {
		width: 100%;
		display: grid;
		place-items: center;

		transition: 0.3s ease;
	}

	:global(body.lightMode) .hero-section-container {
		background-color: transparent !important;
	}

	:global(body.lightMode) .content-section {
		background-color: transparent !important;
	}

	.hero-section {
		display: flex;
		align-items: center;
		flex-direction: flex-start;
		height: 30vh;
	}

	.content-section {
		overflow-y: auto;
		display: grid;
		place-items: center;

		transition: transform 0.3s ease;
	}

	.content-section .search-container {
		display: grid;
		place-items: center;
		width: 100%;
		padding-bottom: 150px;
		z-index: 10;
	}

	.hero-container {
		position: relative;
		width: 100%;
		max-width: 100%;
		z-index: 10;
	}

	.search-container {
		position: relative;
		display: flex;
		justify-content: center;
		align-items: center;
	}

	.search-bar {
		padding: 10px;
		font-size: 16px;
	}

	:global(body) .search-container::before {
		content: '';
		position: absolute;
		top: 15%;
		width: 350px;
		height: 1.5px;
		background: var(--lightColor);
		transform: translateY(-50%);
	}

	:global(body.lightMode) .search-container::before {
		background: var(--darkColor);
	}

	:global(body) .search-container::after {
		content: '';
		position: absolute;
		top: 15%;
		width: 350px;
		height: 1.5px;
		background: var(--lightColor);
		transform: translateY(-50%);
	}

	:global(body.lightMode) .search-container::after {
		background: var(--darkColor);
	}

	.search-container::before {
		left: 0;
	}

	.search-container::after {
		right: 0;
	}

	.content-container {
		width: 60%;
		margin: auto;
		padding: 20px;
	}

	:global(body) #searchInput {
		width: 100%;
		padding: 20px;

		border-radius: 15px;
		border: none;
		background-color: transparent !important;

		transition: 0.5s ease;
	}

	:global(body) #searchInputScrolled,
	:global(body) #searchInput:focus {
		box-shadow: inset 8px 8px 8px -1px rgba(0, 0, 0, 0.4),
			inset -8px -8px 8px -1px rgba(56, 56, 56, 0.4);

		color: var(--lightColor);
	}

	:global(body.lightMode) #searchInput {
		width: 100%;
		padding: 15px;

		border-radius: 15px;
		border: none;
		background-color: transparent !important;
		transition: 0.5s ease;
	}

	:global(body.lightMode) #searchInputScrolled,
	:global(body.lightMode) #searchInput:focus {
		box-shadow: inset 10px 10px 10px -1px rgba(0, 0, 0, 0.1),
			inset -10px -10px 10px -1px rgba(255, 255, 255, 0.3);

		color: var(--darkColor);
	}

	:global(body) #searchInput::placeholder {
		color: var(--lightColor);
		font-weight: 300;
		font-size: 17px;
	}

	:global(body.lightMode) #searchInput::placeholder {
		color: var(--darkColor);
	}

	.cards-container {
		display: grid;
		grid-template-columns: repeat(4, 1fr);
		gap: 40px;
	}

	:global(body) .card {
		background-color: transparent;
		border-radius: 20px;
		aspect-ratio: 1 / 1;
		overflow: hidden;

		display: flex;
		flex-direction: column;
		align-items: flex-start;
		justify-content: flex-end;
		position: relative;

		padding: 10px;
		opacity: 0.9;
		z-index: 10;

		transition: 0.5s ease;
	}

	:global(body) .cardTopScroll {
		box-shadow: none;
		border: 1.5px solid var(--lightColor);
	}

	:global(body.lightMode) .cardTopScroll {
		box-shadow: none;
		border: 1.5px solid var(--darkColor);
	}

	:global(body) .cardScrolled {
		box-shadow: 10px 10px 10px -1px rgba(0, 0, 0, 0.4), -10px -10px 10px -1px rgba(56, 56, 56, 0.4); /* Neumorphic shadow with positive and negative values */
	}

	:global(body.lightMode) .cardScrolled {
		box-shadow: 10px 10px 10px -1px rgba(0, 0, 0, 0.1),
			-10px -10px 10px -1px rgba(255, 255, 255, 0.3);
	}

	:global(body) .card::before {
		content: '';
		position: absolute;
		top: 0;
		left: 0;
		width: 100%;
		height: 100%;
		border-radius: inherit;
		box-shadow: inset 10px 10px 10px -1px rgba(0, 0, 0, 0.4),
			inset -10px -10px 10px -1px rgba(56, 56, 56, 0.4);
		opacity: 0;
		transition: opacity 0.25s ease;
	}

	:global(body.lightMode) .card::before {
		content: '';
		position: absolute;
		top: 0;
		left: 0;
		width: 100%;
		height: 100%;
		border-radius: inherit;
		box-shadow: inset 10px 10px 10px -1px rgba(0, 0, 0, 0.1),
			inset -10px -10px 10px -1px rgba(255, 255, 255, 0.3);
		opacity: 0;
		transition: opacity 0.25s ease;
	}

	:global(body) .card:hover::before {
		opacity: 1;
	}

	:global(body.lightMode) .card:hover::before {
		opacity: 1;
	}

	:global(body.lightMode) .card {
		border-radius: 20px;
		aspect-ratio: 1 / 1;
		overflow: hidden;

		display: flex;
		flex-direction: column;
		align-items: flex-start;
		justify-content: flex-end;
		position: relative;

		padding: 10px;
		opacity: 0.9;
		z-index: 10;

		transition: box-shadow 0.5s ease, outline 0.5s ease, background-color 0.5s ease;
	}

	:global(body) .card:hover {
		box-shadow: none;
	}

	:global(body.lightMode) .card:hover {
		box-shadow: none;
	}

	.card:last-child {
		margin-bottom: 70px;
	}

	.product-image {
		width: 70%;
		margin: 0 auto;
		align-self: center;
		max-height: 50%;
		object-fit: contain;
		position: absolute;
		top: 30px;

		transition: 0.25s ease;
	}

	.card:before {
		content: '';
		width: 100%;
		padding-top: 100%;
		display: block;
	}

	.brand-name,
	.company-name {
		align-self: flex-start;
		margin-left: 10px;

		transition: 0.25s ease;
	}

	.brand-name {
		font-size: 18px;
	}

	.company-name {
		margin-bottom: 5px;
		font-size: 14px;
		opacity: 0.6;
	}

	@media (max-width: 450px) {
		.cards-container {
			grid-template-columns: 1fr;
		}

		.search-container::before,
		.search-container::after {
			display: none;
		}

		.hero-container {
			transform: scale(0.9);
		}

		.content-container {
			width: 75%;
		}

		.content-section .search-container {
			padding-bottom: 110px;
		}
	}
</style>
