<script>
  import Header from "./Header.svelte";
  import "./styles.css";
  import PageTransition from "./transition.svelte";

  export let data;
</script>

<div class="app">
  <Header />

  <main>
    <PageTransition url={data.url}>
      <slot />
    </PageTransition>
  </main>
</div>
