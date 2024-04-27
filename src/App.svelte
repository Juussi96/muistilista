<script>
  import KirjoitusIkkuna from './lib/KirjoitusIkkuna.svelte';
  import Tervetuloa from './lib/Tervetuloa.svelte';
  import { scale, fly } from 'svelte/transition';

  let annettuNimi = '';
  let annettuSyote = '';

  let kayttajaTunnus = null;

  let tunnus = '';
  let salis = '';

  let visited = false;
  let virheViestiTunnus = '';
  let virheViestiSalasana = '';

  // Tarkistaa onko "tunnus" kelvollinen
  const kelvollinenTunnus = () => {
    if (tunnus.length === 0 && visited) {
      virheViestiTunnus = 'Kenttä ei voi olla tyhjä';
    } else {
      virheViestiTunnus = '';
    }
  };

  // Tarkistaa onko "salasana" kelvollinen
  const kelvollinenSalasana = () => {
    if (!salis && visited) {
      virheViestiSalasana =
        'Salasana ei voi olla tyhjä ja se on väh. 8 merkkiä pitkä';
    } else if (salis.length < 8 && visited) {
      virheViestiSalasana = 'Salasanan tulee olla vähintään 8 merkkiä pitkä';
    } else {
      virheViestiSalasana = '';
    }
  };

  // Kun käyttäjä on "vieraillut" tunnus inputissa, suoritetaan tämä funktio
  // katsotaan onko käyttäjä jättänyt ruudun tyhjäksi ja onko syöte tarpeeksi pitkä
  const vierailtuTunnus = () => {
    visited = true;
    kelvollinenTunnus();
  };
  // Kun käyttäjä on "vieraillut" salasana inputissa, suoritetaan tämä funktio
  const vierailtuSalasana = () => {
    visited = true;
    kelvollinenSalasana();
  };

  let listat = [];
  let valittuLista = null;

  let naytaKirjautumis = true;
  let naytaIkkuna = false;
  let naytaVirhe = false;
  let naytaSisalto = false;
  let virheKirjaudu = false;

  let tervetuloaLaatikko = false;

  const lisaaLista = function () {
    // Tarkistetaan että annettunimi muuttujalle syötetty tieto ei ole tyhjä tai liian pitkä.
    if (annettuNimi.length > 0 && annettuNimi.length <= 30) {
      listat = [
        ...listat,
        {
          nimi: annettuNimi,
          muistiinpanot: [],
        },
      ];
      naytaVirhe = false;
      annettuNimi = '';
    } else {
      naytaVirhe = true;
    }
  };

  // Valitsee kortin ja näyttää sille liittyvän kirjoitusikkunan
  const valitseKortti = (kortti) => {
    valittuLista = kortti;
    naytaIkkuna = true;
  };

  // Kirjaudutaan sisään tarkistamalla annettu tunnus ja salasana
  const kirjauduSis = function () {
    if (tunnus.length > 0 && salis.length >= 8) {
      naytaSisalto = true;
      kayttajaTunnus = tunnus;
      kayttajaTunnus = kayttajaTunnus;
      tervetuloaLaatikko = true;
      setTimeout(() => {
        tervetuloaLaatikko = false;
      }, 4000);

      naytaKirjautumis = false;
      virheKirjaudu = false;
      tunnus = '';
      salis = '';
    } else {
      virheKirjaudu = true;
      salis = '';
    }
  };

  // Tämä funktio välitetään KirjoitusIkkunaan.svelte tiedostolle.
  // Tämä funktio lisää valittuListan muistiinpanot taulukkoon objektin jonka ominaisuus on
  // nimeltään sisalto, jonka arvona on annettuSyote, jonka käyttäjä antaa input ruutuun.
  const tallennaMuistiinpano = () => {
    valittuLista.muistiinpanot = [
      ...valittuLista.muistiinpanot,
      {
        sisalto: annettuSyote,
      },
    ];
    listat = [...listat];
    annettuSyote = '';
  };
  // Tämä funktio välitetään KirjoitusIkkunaan.svelte tiedostolle.
  // Tämä funktio "poistaa" kyseisen listan joka on valittuna.
  // Filter käy läpi listat taulukon ja vertaa alkiota nimeltä lista valittuun, jos se on sama se poistetaan.
  const poistaKortti = (poistettava) => {
    valittuLista = poistettava;
    listat = listat.filter((lista) => lista !== poistettava);
  };
</script>

<main>
  {#if kayttajaTunnus !== null && tervetuloaLaatikko}
    <Tervetuloa annettuTunnus={kayttajaTunnus} />
  {/if}

  <div class="otsikko">
    <h1>LiT</h1>
    <h3>List your Things</h3>
  </div>

  <!-- Tässä tein kirjautumis ikkunan, jos kummassakaan ei ole syötettä niin tulee virhe ilmoitus -->
  {#if naytaKirjautumis}
    <hr />
    <div class="kirjauduIkkuna">
      <div class="syote">
        <h3 style="text-decoration: underline;">Kirjaudu sisään</h3>
        <p>Käyttäjätunnus</p>
        <input
          type="string"
          placeholder="Tunnus"
          bind:value={tunnus}
          on:blur={vierailtuTunnus}
          on:input={kelvollinenTunnus}
        />
        {#if virheViestiTunnus}
          <p style="color: red; font-size: 0.8rem;">
            {virheViestiTunnus}
          </p>
        {/if}
        <p>Salasana</p>
        <input
          type="password"
          placeholder="********"
          on:blur={vierailtuSalasana}
          on:input={kelvollinenSalasana}
          bind:value={salis}
        />
        {#if virheViestiSalasana}
          <p style="color: red; font-size: 0.8rem;">
            {virheViestiSalasana}
          </p>
        {/if}
      </div>
      <button on:click={kirjauduSis} class="nappi">Kirjaudu</button>

      {#if virheKirjaudu}
        <p
          style="color: red;
          font-size: 0.8rem;"
          in:fly|global={{ y: 50, duration: 500 }}
          out:fly={{ x: 500, duration: 1000 }}
        >
          Antamasi tiedot ovat väärin!
          <br />(Psst! salasana on väh. 8 merkkiä pitkä)
        </p>
      {/if}
    </div>
  {/if}

  {#if naytaSisalto}
    <hr />
    <div class="rakenne">
      <div>
        <!-- Tässä luodaan uusi lista -->
        <h6 class="syotePalkki">
          Listan nimi:
          <input
            type="string"
            placeholder="esim... Koti"
            bind:value={annettuNimi}
          />
          <button on:click={lisaaLista} class="nappi" style="font-size: 0.7rem;"
            >Lisää</button
          >
        </h6>

        {#if naytaVirhe}
          <!-- Virhe ilmoitus -->
          <p
            class="warning"
            in:fly|global={{ y: 50, duration: 500 }}
            out:fly={{ x: 500, duration: 1000 }}
          >
            Nimen täytyy olla Vähintään 1-30 merkkiä
          </p>
        {/if}
      </div>
    </div>
  {/if}

  <div class="container">
    {#each listat as lista (lista)}
      <div
        class="kortti"
        in:scale={{ duration: 300 }}
        out:fly={{ x: 250, duration: 1000 }}
      >
        <div class="kortinOtsikko">
          <button class="poista" on:click={() => poistaKortti(lista)}>X</button>
          <h5>
            {lista.nimi}
          </h5>

          <!-- Valittulistan arvoksi tulee klikattu lista -->
          <button on:click={() => valitseKortti(lista)}>Kirjoita...</button>
        </div>

        {#each lista.muistiinpanot as muistiinpano}
          <div transition:scale={{ duration: 500 }}>
            <p class="listaus">
              - {muistiinpano.sisalto}
            </p>
          </div>
        {/each}
      </div>
    {/each}
  </div>

  {#if naytaIkkuna}
    <KirjoitusIkkuna
      listanNimi={valittuLista}
      bind:annettuSyote
      on:tallenna={tallennaMuistiinpano}
      sulje={() => (naytaIkkuna = false)}
    />
  {/if}
</main>

<style>
  main {
    max-width: 100%;
    margin-top: 3rem;
    color: black;
  }
  input {
    width: fit-content;
    height: 0.9rem;
  }

  .syote p {
    margin: 0;
  }
  .syote input {
    width: 15rem;
  }

  .rakenne {
    position: relative;
    left: 0.5rem;
    top: -3rem;
  }

  .otsikko h3 {
    margin-top: -2.5rem;
  }

  .warning {
    color: red;
    font-size: 0.7rem;
    font-weight: 500;
    text-decoration: underline;
    position: absolute;
    top: 2.7rem;
    right: 40%;
  }
  .container {
    display: grid;
    grid-template-columns: 10fr 10fr 10fr;
    grid-template-rows: auto;
    gap: 10px;
    margin-left: 0.5rem;
    margin-right: 0.5rem;
  }
  h5 {
    margin: 0;
    position: relative;
    top: -2.2rem;
  }

  .syotePalkki {
    margin-bottom: 0rem;
    margin-top: 4rem;
  }

  .kortti {
    grid-column: auto;
    grid-row: auto;
    padding: 0.2rem;
    background-color: rgb(255, 255, 255);
    border: 1px solid black;
    box-shadow: 5px 10px #505050;
    border-radius: 0.2rem;
    height: fit-content;
    margin-bottom: 2.5rem;
  }
  .kortinOtsikko {
    background-color: #ac7b7d;
    border-radius: 0.2rem;
  }
  .kortinOtsikko h5 {
    text-transform: capitalize;
    font-size: 1.05rem;
  }
  button {
    font-weight: 550;
  }

  .kortti button.poista {
    display: fixed;
    position: relative;
    opacity: 100%;
    font-family: 'Gill Sans', 'Gill Sans MT', Calibri, 'Trebuchet MS',
      sans-serif;
    font-weight: 100;
    top: -0.8rem;
    right: -40%;
    color: black;
    background-color: #ffffff;
    border: 1px solid black;
    margin-top: 1rem;
  }

  .kortti button.poista:hover {
    background-color: red;
    transition: 0.5s;
    color: white;
  }

  .nappi {
    margin-top: 1rem;
    background-color: #ac7b7d;
    border: black 0.2px solid;
  }

  .kortti button {
    background-color: #ac7b7d;
    font-size: 0.8rem;
    color: rgb(54, 54, 54);
    position: relative;
    background-color: #866364;
    top: -1.5rem;
  }

  .kortti button:hover {
    color: red;
    transition: 0.5s;
  }
  .listaus {
    font-size: 0.8rem;
    color: black;
    font-weight: 300;
    margin: 0;
  }
  .listaus:hover {
    font-weight: 500;
    transition: 0.7s;
  }
</style>
