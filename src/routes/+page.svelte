<script>
	import dataTree from '$lib/data.json';
	import { slide } from 'svelte/transition';

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

	let searchQuery = '';
	let filteredData = filterData(dataTree[0]);

	$: if (searchQuery) {
		searchData();
	} else {
		filteredData = filterData(dataTree[0]);
	}

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
										let match = brand.brand.toLowerCase().includes(searchQueryLower);
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

	let isAccordionOpen = false;

	let accordionElement; // Reference to the accordion content DOM element
	let accordionHeight = '0px'; // Initialize accordion height

	function toggleAccordion() {
		isAccordionOpen = !isAccordionOpen;

		if (isAccordionOpen) {
			// Ensure the element is rendered in the DOM before measuring its height
			setTimeout(() => {
				accordionHeight = `${accordionElement.scrollHeight}px`;
			}, 0);
		} else {
			accordionHeight = '0px';
		}
	}

	import { onMount } from 'svelte';

	let isAtTop = true;

	// Run this code when the component mounts
	onMount(() => {
		function handleScroll() {
			isAtTop = window.pageYOffset <= 10;
		}

		// Listen for scroll events
		window.addEventListener('scroll', handleScroll);

		// Initial check
		handleScroll();

		return () => {
			// Remove listener when the component is destroyed
			window.removeEventListener('scroll', handleScroll);
		};
	});
</script>

<main>
	<div class="searchSectionContainer">
		<section class="search-section">
			<div class="search-container">
				<h2>Switch to local, where possible.</h2>
				<button class="accordion-toggle" on:click={toggleAccordion}>
					{isAccordionOpen ? '- Hide Description' : '+ Show Description'}
				</button>

				{#if isAccordionOpen}
					<div
						bind:this={accordionElement}
						class="accordion-content {isAccordionOpen ? 'active' : ''}"
						transition:slide
					>
						<p>
							Choosing to boycott common products is a powerful stand for justice. Every small act
							matters in our global solidarity with Palestine.
						</p>
						<p>
							While not every item can be replaced locally, each conscious choice to prefer local
							alternatives over international brands is a step towards change.
						</p>
						<p>
							Our collective actions, seemingly small, can lead to significant impact. With every
							meal or product we switch, we contribute to a larger movement that values human rights
							and dignity.
						</p>
						<p>
							Let's stand together, making mindful choices that echo our support for peace and
							justice. Your choice has power – wield it with wisdom and compassion.
						</p>

						<!-- <a class="action-button" href="/about">How every effort counts ></a> -->
					</div>
				{/if}
			</div>
		</section>
	</div>

	<section
		class="content-section"
		style="transform: translateY({isAccordionOpen ? accordionHeight : '0px'})"
	>
		<div class="searchBar">
			<input
				type="text"
				id="searchInput"
				class="search-bar {isAtTop ? 'searchTopScroll' : 'searchInputScrolled'}"
				bind:value={searchQuery}
				placeholder="Find alternatives..."
			/>
		</div>

		<div class="content-container">
			{#if filteredData && Object.keys(filteredData).length > 0}
				{#each Object.entries(filteredData) as [heading, subheadings]}
					<div>
						<h3>{heading}</h3>
						{#each Object.entries(subheadings) as [subheading, titles]}
							<div>
								<h4>{subheading}</h4>
								{#each Object.entries(titles) as [title, items]}
									<div>
										<h5>> {title}</h5>
										<div class="cards-container">
											{#each items as item, index}
												<div class={isAtTop ? 'card cardTopScroll' : 'card cardScrolled'}>
													<img class="product-image" src={item.image} alt="{item.brand} product" />
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
</main>

<style>
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

	.accordion-toggle {
		background-color: #f2f2f2;
		border-radius: 15px;
		border: 1px solid #d1d1d1;
		padding: 10px 20px;
		font-size: 16px;
		color: #333;
		cursor: pointer;
		outline: none;
		z-index: 0;
	}

	.accordion-content p {
		margin-bottom: 15px;
		line-height: 1.9;
		font-size: 16px;
	}

	.accordion-content p:last-child {
		margin-bottom: 0;
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

	:global(body) .accordion-content {
		position: absolute;
		max-width: 97%;
		box-sizing: border-box;
		overflow: hidden;
		background-color: var(--darkColor);
		color: var(--lightColor);

		border-radius: 15px;
		margin-top: 10px;
		padding: 20px;
		font-size: 16px;

		display: none;
	}

	:global(body.lightMode) .accordion-content {
		background-color: var(--lightColor);
		color: var(--darkColor);
	}

	.accordion-content.active {
		display: block;
		width: 100%;
	}

	.content-container h3 {
		font-size: 28px;
		font-weight: 600;
		padding-bottom: 5px;
	}

	.content-container h4 {
		font-size: 21px;
		font-weight: 500;
		padding-bottom: 5px;
	}

	.content-container h5 {
		font-size: 16px;
		font-weight: 500;
		padding-bottom: 20px;
	}

	/* body {
		margin: 0;
		padding: 0;
		display: flex;
		flex-direction: column;
		height: 100vh;
	} */

	.searchSectionContainer {
		width: 100%;
		display: grid;
		place-items: center;

		transition: 0.3s ease;
	}

	:global(body.lightMode) .searchSectionContainer {
		background-color: transparent !important;
	}

	:global(body.lightMode) .content-section {
		background-color: transparent !important;
	}

	.search-section {
		display: flex;
		align-items: center;
		flex-direction: flex-start;
		height: 30vh;
		width: 63%;
	}

	.search-container h2 {
		padding: 20px 0px;
		font-weight: 600;
		font-size: 30px;
	}

	.content-section {
		overflow-y: auto;
		display: grid;
		place-items: center;

		transition: transform 0.3s ease;
	}

	.content-section .searchBar {
		width: 500px;
		padding: 50px 0px;
		z-index: 10;
	}

	.search-container {
		position: relative;
		width: 100%;
		max-width: 100%;
		padding: 20px;
		z-index: 10;
	}
	.content-section-open .accordion-content {
		max-height: 500px;
		opacity: 1;
	}

	.content-section-open {
		transform: translateY(100%);
	}
	.searchBar {
		position: relative;
		display: flex;
		justify-content: center;
		align-items: center;
	}

	.search-bar {
		padding: 10px;
		font-size: 16px;
	}

	:global(body) .searchBar::before {
		content: '';
		position: absolute;
		top: 50%;
		width: 300px;
		height: 1.5px;
		background: var(--lightColor);
		transform: translateY(-50%);
	}

	:global(body.lightMode) .searchBar::before {
		background: var(--darkColor);
	}

	:global(body) .searchBar::after {
		content: '';
		position: absolute;
		top: 50%;
		width: 300px;
		height: 1.5px;
		background: var(--lightColor);
		transform: translateY(-50%);
	}

	:global(body.lightMode) .searchBar::after {
		background: var(--darkColor);
	}

	.searchBar::before {
		left: 0;
		margin-left: -330px;
	}

	.searchBar::after {
		right: 0;
		margin-right: -330px;
	}

	.content-container {
		width: 80%;
		max-width: 1200px;
		margin: auto;
		padding: 20px;
	}

	:global(body) #searchInput {
		width: 100%;
		padding: 15px;

		border-radius: 15px;
		border: none;
		background-color: transparent !important;

		transition: 0.5s ease;
	}

	:global(body) #searchInputScrolled,
	:global(body) #searchInput:focus {
		box-shadow: inset 8px 8px 8px -1px rgba(0, 0, 0, 0.4),
			inset -8px -8px 8px -1px rgba(56, 56, 56, 0.4);
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
	}

	.company-name {
		margin-bottom: 5px;
		font-size: 14px;
	}

	@media (max-width: 1200px) {
		.cards-container {
			grid-template-columns: repeat(3, 1fr);
		}
	}

	@media (max-width: 900px) {
		.cards-container {
			grid-template-columns: repeat(2, 1fr);
		}
	}

	@media (max-width: 600px) {
		.cards-container {
			grid-template-columns: 1fr;
		}
	}
</style>
