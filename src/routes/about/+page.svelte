<script>
	import 'iconify-icon';
	import shahum from '$lib/products/barley.png';
	import '../styles.css';
	import gsap from 'gsap';
	import { onMount } from 'svelte';

	onMount(async () => {
		gsap.from('.tools, .about, .heading', {
			y: 1000,
			opacity: 0,
			duration: 0.5,
			ease: 'power4.out'
		});

		gsap.from('li', {
			y: 100,
			opacity: 0,
			duration: 0.5,
			stagger: 0.05,
			ease: 'power4.out'
		});
	});
</script>

<svelte:head>
	<title>About | Shahum M.</title>
	<meta
		name="description"
		content="A website crafted to serve as a habit tracker for me, encouraging me to indulge in a plethora of delightful experiences that I love and in the process, be inspired to document them."
	/>
</svelte:head>

<nav>
	<div class="tools">
		<div class="heading">
			<h1>Tools</h1>
		</div>

		<ul>
			<li><iconify-icon icon="devicon:html5" /> HTML</li>
			<li><iconify-icon icon="devicon:css3" /> CSS</li>
			<li><iconify-icon icon="logos:javascript" /> JavaScript</li>
			<li><iconify-icon icon="logos:svelte-icon" /> SvelteKit</li>
			<li><iconify-icon icon="logos:greensock-icon" /> GSAP</li>
			<li>
				<iconify-icon icon="akar-icons:vercel-fill" style="color: lightgray;" />
				Vercel
			</li>
			<li><iconify-icon icon="devicon:figma" /> Figma</li>
		</ul>

		<div class="mobileFooter">
			<a href="mailto:hello@shahum.art">hello@shahum.art</a>
		</div>
	</div>

	<div id="nav-links">
		<div class="heading">
			<img src={shahum} alt="Shahum M." loading="eager" />
			<div style="font-size: 20px; font-weight: bold; margin: 10px;">PEEK-A-BOO</div>

			<h1>I'm Shahum!</h1>
			<p class="email-link">
				<a href="mailto:hello@shahum.art">hello@shahum.art</a>
			</p>

			<div class="socialmedia">
				<a href="http://www.instagram.com/galacticsapien">
					<div class="socialicon">
						<iconify-icon icon="line-md:instagram" style="color: white;" width="34" height="34" />
					</div>
				</a>

				<a href="http://www.github.com/shahumm">
					<div class="socialicon">
						<iconify-icon icon="line-md:github-loop" style="color: white;" width="33" height="33" />
					</div>
				</a>

				<a href="http://www.goodreads.com/shahum">
					<div class="socialicon">
						<iconify-icon icon="fa-brands:goodreads" style="color: white;" width="32" height="32" />
					</div>
				</a>
			</div>
		</div>
	</div>

	<div class="about">
		<div class="heading">
			<h1>About</h1>
		</div>
		<p>
			I occasionally have a strong urge to immerse myself in creative activities such as coding,
			illustrating, photography, animation, or just exploring the nearby hills in search of unique
			birds.<br /><br />However, the spontaneity of these urges and their remarkably therapeutic
			effects have left me yearning for their recurrence.<br /><br />As a result, I crafted this
			website that (I hope) serves as a habit tracker, encouraging me to indulge in a plethora of
			delightful experiences that I love and in the process, be inspired to document them. :)
		</p>
	</div>
</nav>

<style>
	/* Social Icons */
	.socialmedia {
		display: flex;
		align-items: center;
		justify-content: center;
		flex-direction: row;
		margin-top: 110px;
		cursor: pointer;

		padding: 10px 13px;
		transition: 0.2s ease;
		border-radius: 8px;
	}

	.socialmedia .socialicon {
		transition: 0.2s ease;
		border-radius: 8px;

		padding: 0px 16px;
		padding-top: 5px;

		transform: scale(0.85);
	}

	.socialmedia .socialicon:hover {
		transform: scale(1);
	}

	.email-link a {
		color: var(--darkColor);
		text-decoration: none;
		cursor: pointer;

		padding: 10px 13px;
		transition: 0.2s ease;
		border-radius: 8px;
		border: 1.5px solid var(--darkColor);
	}

	.email-link a:hover {
		background-color: var(--darkColor);
		border-radius: 8px;
		color: var(--lightColor);
	}

	:global(body.lightMode) .email-link a {
		color: var(--lightColor);
		border: 1.5px solid var(--lightColor);
	}

	:global(body.lightMode) .email-link a:hover {
		color: var(--darkColor);
		background-color: var(--lightColor);
	}

	.mobileFooter {
		display: none;
	}

	* {
		font-weight: 500 !important;
	}

	ul {
		list-style-type: none;
	}

	ul li {
		margin-bottom: 5px;
		letter-spacing: 1px;
	}

	nav {
		height: 101%;
		width: 100%;
		position: absolute;
		left: 0px;
		top: 50%;
		transform: translateY(-50%);
		z-index: -1;

		display: grid;
		place-items: center;
		grid-template-columns: 1fr 1fr 1fr;
		grid-template-rows: auto;
		grid-template-areas: 'first second third';
	}

	#nav-links,
	.tools,
	.about {
		height: 100%;
		width: 100%;
		display: flex;
		flex-direction: column;
		text-align: center;
	}

	#nav-links {
		grid-area: second;

		justify-content: center;
		align-items: center;
		padding-top: 25px;

		border-left: 1px solid;
		border-right: 1px solid;
	}

	#nav-links .heading {
		text-align: center;
		font-weight: 300;

		width: 100%;
	}

	#nav-links .heading h1 {
		padding-bottom: 20px;
		border-bottom: 1px solid;
		font-size: 30px;
	}

	.about {
		grid-area: third;
		justify-content: top;
		align-items: center;
	}

	.tools {
		grid-area: first;
		justify-content: top;
		align-items: center;
	}

	#nav-links img {
		margin-bottom: 10px;
		border-radius: 200px;
		width: 300px;
	}

	#nav-links p {
		position: relative;
		top: 15%;
	}

	.tools .heading,
	.about .heading {
		border-bottom: 1px solid;
		width: 100%;
		padding: 10px;
		padding-top: 150px;
	}

	.about p,
	.tools ul {
		text-align: left;
		padding: 30px;
		line-height: 1.8;
	}

	@media (max-width: 1000px) {
		.socialmedia {
			margin-top: 5px;
			transform: scale(0.9);
		}

		.mobileFooter {
			width: 100%;
			color: var(--darkColor);
			height: 90px;
			display: grid;
			place-items: center;
			margin-bottom: 50px;
		}

		.mobileFooter a {
			color: var(--darkColor);
			text-decoration: none;
			cursor: pointer;

			padding: 10px 13px;
			transition: 0.2s ease;
			border-radius: 8px;
			border: 1.5px solid var(--darkColor);
		}

		.mobileFooter a:hover {
			background-color: var(--darkColor);
			border-radius: 8px;
			color: var(--lightColor);
		}

		:global(body.lightMode) .mobileFooter a {
			color: var(--lightColor);
			border-color: var(--lightColor);
		}
		:global(body.lightMode) .mobileFooter a:hover {
			color: var(--darkColor);
			background-color: var(--darkBackground);
		}

		.about p {
			font-size: 18px;
			line-height: 1.9;
		}

		nav {
			grid-template-columns: 1fr;
			grid-template-rows: auto;
			grid-template-areas: 'second' 'third' 'first';
		}

		#nav-links {
			border: none;
			padding-top: 300px;
		}

		#nav-links img {
			max-width: 260px;
		}

		#nav-links p {
			display: none;
		}

		#nav-links .heading h1 {
			border: none;
			font-size: 26px;
			padding: 0;
		}

		.about .heading {
			padding-top: 0px;
		}

		.tools .heading {
			padding-top: 10px;
		}

		#nav-links {
			position: relative;
			bottom: 100px;
		}

		#nav-links p {
			width: 100%;
		}
	}
</style>
