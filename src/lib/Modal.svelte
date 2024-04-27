<script>
  import { fly } from 'svelte/transition';
</script>

<div class="backdrop" />
<!-- Tämä on sama modal ikkuna mikä oli pizza tehtävissä, hiukan muokattuna -->
<div
  class="modal"
  in:fly|global={{ y: -1000, duration: 750 }}
  out:fly|global={{ y: 1000, duration: 750 }}
>
  <header>
    <slot name="header" />
  </header>
  <slot />

  <footer>
    <slot name="footer" />
  </footer>
</div>

<style>
  header {
    text-decoration: underline;
    margin-bottom: 0;
    background-color: #ac7b7d;
    padding-top: 0.2rem;
    border-radius: 0.3rem;
  }
  .backdrop {
    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
    height: 100vh;
    background: rgba(0, 0, 0, 0.435);
    z-index: 10;
  }

  .modal {
    font-size: 2rem;
    padding: 1rem;
    position: fixed;
    top: 2vh;
    left: 25vw;
    width: 50%;
    max-height: 80vh;
    background: #ffffff;
    border-radius: 5px;
    z-index: 100;
    box-shadow: 0 2px 8px rgba(0, 0, 0, 0.26);
    overflow: scroll;
  }
</style>
