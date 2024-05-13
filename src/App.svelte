<script>
  import svelteLogo from './assets/svelte.svg'
  import viteLogo from '/vite.svg'
  import Counter from './lib/Counter.svelte'
  import SpaceImages from './lib/SpaceImages.svelte';
</script>

<main> 
  <h1 class="title">NASA Space Images</h1>
   <SpaceImages />
   <h4 class="subtitle">Built with Svelte + Vite</h4>
  
</main>

<style>
  .logo {
    height: 6em;
    padding: 1.5em;
    will-change: filter;
    transition: filter 300ms;
  }
  .title, .subtitle {
    color: #ff3e00;
  }
  .logo:hover {
    filter: drop-shadow(0 0 2em #646cffaa);
  }
  .logo.svelte:hover {
    filter: drop-shadow(0 0 2em #ff3e00aa);
  }
 
</style>
