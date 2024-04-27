<script>
  import { scale } from 'svelte/transition';
  export let annettuTunnus;
</script>

<main>
  <div class="tervetuloa" transition:scale={{ duration: 750 }}>
    <h4>Tervetuloa!</h4>
    <h6>{annettuTunnus}</h6>
  </div>
</main>

<style>
  .tervetuloa {
    position: absolute;
    top: 2.5rem;
    left: 1rem;
    background-color: #f7f7f7cc;
    border: #505050 1px solid;
    width: 8rem;
    border-radius: 1rem;
    padding-left: 1rem;
    padding-right: 1rem;
  }
  .tervetuloa h4,
  h6 {
    font-size: 0.9rem;
    margin-top: 0.5rem;
    margin-bottom: 0;
  }
</style>
