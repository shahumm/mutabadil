<script>
  import gsap from "gsap";
  import { onMount, afterUpdate } from "svelte";

  export let isOpen;
  export let closeMenu;

  const handleItemClick = () => {
    closeMenu();
  };

  const animateMenuItems = () => {
    const menuItems = document.querySelectorAll(
      ".overlay-menu .menu-item, .sub-heading"
    );

    gsap.fromTo(
      menuItems,
      {
        x: 100,
        opacity: 0,
      },
      {
        x: 0,
        opacity: 1,
        duration: 0.05,
        stagger: 0.1,
        ease: "power4.out",
      }
    );
  };

  onMount(() => {
    if (isOpen) {
      animateMenuItems();
    }
  });

  afterUpdate(() => {
    if (isOpen) {
      animateMenuItems();
    }
  });
</script>

<div
  class="overlay-menu"
  style={isOpen ? "transform: translateY(0);" : "transform: translateY(100%);"}
>
  <p class="sub-heading">NAVIGATE</p>
  <a class="menu-item" href="/" on:click={handleItemClick}>Artworks</a>
  <a class="menu-item" href="/webcomic" on:click={handleItemClick}>Webcomic</a>
  <a class="menu-item" href="/about" on:click={handleItemClick}>About</a>

  <p class="sub-heading">CONNECT</p>
  <a
    class="menu-item smaller"
    href="https://www.instagram.com/galacticsapien"
    on:click={handleItemClick}>Instagram</a
  >
  <a
    class="menu-item smaller"
    href="mailto:hello@shahum.art"
    on:click={handleItemClick}>hello@shahum.art</a
  >
</div>

<style>
  .overlay-menu {
    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    background-color: rgb(16, 16, 16);
    display: flex;
    flex-direction: column;
    align-items: flex-end;
    justify-content: center;
    padding: 20px;
    box-sizing: border-box;
    z-index: 800;
    transition: transform 0.25s ease;
  }

  .overlay-menu .menu-item {
    color: white;
    font-size: 36px;
    text-decoration: none;
    margin-bottom: 30px;
    margin-right: 100px;

    transition: 0.25s ease;
  }

  .overlay-menu .menu-item:hover {
    color: rgb(168, 190, 255);
  }

  .overlay-menu .smaller {
    font-size: 24px;
    margin-bottom: 20px;
  }

  .overlay-menu .smaller:hover {
    color: rgb(178, 255, 168);
  }

  .overlay-menu .sub-heading {
    color: gray;
    font-size: 16px;
    text-decoration: none;
    margin-top: 50px;
    margin-bottom: 20px;
    margin-right: 100px;

    transition: 0.25s ease;
  }

  /* Light Mode */
  :global(body.lightMode) .overlay-menu {
    background-color: rgb(231, 231, 231);
  }

  :global(body.lightMode) .overlay-menu .menu-item {
    color: rgb(40, 40, 40);
  }

  @media (max-width: 768px) {
    .overlay-menu {
      align-items: flex-end;
    }

    .overlay-menu .sub-heading,
    .overlay-menu .menu-item {
      margin-right: 20px;
    }

    .overlay-menu .menu-item {
      font-size: 24px;
    }

    .overlay-menu .sub-heading {
      font-size: 14px;
    }

    .overlay-menu .smaller {
      font-size: 16px;
    }
  }
</style>
