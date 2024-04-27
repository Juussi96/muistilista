<script>
  import Modal from './Modal.svelte';
  import { createEventDispatcher } from 'svelte';
  import { scale } from 'svelte/transition';

  export let annettuSyote;
  export let sulje;
  export let listanNimi;
  let virhe = false;

  const dispatch = createEventDispatcher();

  // Funktio tallentaa syötetyn muistiinpanon, jos se on kelvollinen
  const tallennaSyote = function () {
    if (annettuSyote.length > 0 && annettuSyote.length <= 30) {
      dispatch('tallenna', annettuSyote);
      virhe = false;
    } else {
      virhe = true;
    }
  };
</script>

<main>
  <Modal>
    <!-- Sulje nappi sulkee modal ikkunan -->
    <button class="delete" on:click={sulje}>Sulje</button>

    <h3 slot="header">Muistiinpanot:</h3>
    <h3 class="nimi">{listanNimi.nimi}</h3>
    <p>
      Kirjoita haluamasi muistiinpanot alla olevaan kenttään ja <strong
        >tallenna!</strong
      >
    </p>

    <div class="kirjoitus">
      <input type="String" bind:value={annettuSyote} />
      <button on:click={tallennaSyote}>Tallenna</button>
      {#if virhe}
        <div transition:scale={{ duration: 500 }}>
          <p class="virhe">Syötteen täytyy olla 1-30 merkkiä pitkä</p>
        </div>
      {/if}
    </div>
  </Modal>
</main>

<style>
  main {
    color: black;
  }
  .delete {
    position: fixed;
    display: block;
    margin: 15px;
    margin-top: -2.5rem;
  }
  .delete {
    border: 0;
    background-color: #ffffff;
  }
  .nimi {
    color: rgb(0, 0, 0);
    font-size: 1.8rem;
    margin-top: -1.5rem;
    background-color: #ac7b7d;
    border-radius: 0.3rem;
  }
  .kirjoitus {
    padding-bottom: 1rem;
  }
  input {
    height: 0.8rem;
  }
  .virhe {
    font-size: 0.65rem;
    color: red;
    padding: 0;
    margin: 0;
  }

  h3,
  p {
    font-size: 1rem;
  }
  button {
    font-size: 0.75rem;
    border: 1px solid black;
    width: fit-content;
    background-color: #ac7b7d;
  }
</style>
