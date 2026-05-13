<script>
  import { onMount } from "svelte";
  import Router from "svelte-spa-router";
  import LogementCard from "./components/LogementCard.svelte";
  import Ajouter from "./components/Ajouter.svelte";
  import Modifier from "./components/Modifier.svelte";
  import "./app.css";
  import Liste from "./pages/Liste.svelte";
  import Detail from "./pages/Detail.svelte";

  const routes = {
    "/": Liste,
    "/detail": Detail,
  };

  let logements = [];

  async function getLogements() {
    try {
      let response = await fetch("/api/logements");
      let data = await response.json();
      logements = data;
    } catch (err) {
      console.error("Erreur lors du chargement des logements:", err);
    }
  }

  onMount(() => {
    getLogements();
  });
;

  /*==================================LES FONCTIONS==================================*/
 
  // Fonction pour supprimer un logement

  let confirmerVisible = false;
  let idASupprimer = null;

  async function supprimerLogement(id) {
    idASupprimer = id;
    confirmerVisible = true;
  }

  async function confirmer() {
    confirmerVisible = false;
    try {
      const response = await fetch(`/api/logements/${idASupprimer}`, {
        method: "DELETE",
      });
      if (!response.ok) throw new Error("Erreur DELETE");
      await getLogements();
    } catch (e) {
      console.error("Erreur lors de la suppression du logement", e);
    } finally {
      idASupprimer = null;
    }
  }

  // Fonction pour mettre à jour un logement
  async function mettreAJourLogement(id, logementMisAJour) {
    try {
      const response = await fetch(`/api/logements/${id}`, {
        method: "PUT",
        headers: { "Content-Type": "application/json" },
        body: JSON.stringify(logementMisAJour),
      });
      if (!response.ok) {
        throw new Error("Erreur PUT");
      }
      await getLogements();
    } catch (e) {
      console.error("Erreur lors de la mise à jour du logement", e);
    }
  }
</script>

<svelte:head>
  <title>GoSki</title>
  <link
    rel="icon"
    href="data:image/svg+xml,<svg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 100 100'><text y='.9em' font-size='90'>🏔️</text></svg>"
  />
</svelte:head>

<!--==================================LE HEADER==================================-->

<header
  class="bg-[#1E293B] text-white px-6 py-4 shadow-md flex flex-col md:flex-row md:items-center md:justify-between gap-4"
>
  <div class="flex flex-col">
    <h1
      class="text-4xl md:text-5xl font-bold text-[#0594D0] tracking-wide font-[Poppins]"
    >
      GoSki
    </h1>
    <p class="text-xs text-[#CBD5E1] font-light">
      Explore les plus beaux séjours
    </p>
  </div>
  <div
    class="flex items-center bg-[#0B1220] border border-[#334155] rounded-lg px-3 py-2 w-full md:w-1/3"
  >
    <input
      type="text"
      placeholder="Rechercher une ville, un logement..."
      class="bg-transparent w-full text-[#E5E7EB] placeholder-[#94A3B8] focus:outline-none"
    />

    <button class="text-[#0594D0] hover:text-[#047AAD] ml-2 text-lg">
      🔍
    </button>
  </div>
</header>

<!-- ============= Confirmation de suppression ============================ -->

{#if confirmerVisible}
  <div
    class="fixed w-full h-full bg-black/50 flex items-center justify-center z-50"
  >
    <div class="bg-white rounded-xl p-6 text-center shadow-xl mx-4">
      <p class="mb-4 text-gray-800">
        Êtes-vous sûr de vouloir supprimer ce logement ?
      </p>
      <div class="flex gap-3 justify-center">
        <button
          class="bg-red-500 text-white px-4 py-2 rounded-lg"
          on:click={confirmer}>Oui</button
        >
        <button
          class="bg-gray-300 px-4 py-2 rounded-lg"
          on:click={() => (confirmerVisible = false)}>Non</button
        >
      </div>
    </div>
  </div>
{/if}
<!--==================================LE MAIN==================================-->

<main class="min-h-screen bg-[#0B1220] p-8 font-sans text-[#E5E7EB]">
  

  <!-- ajout de logement -->
<Ajouter on:ajoute={getLogements} />



  <!-- Affichage des logements -->

  {#if logements.length === 0}
    <p class="text-[#9CA3AF] mt-4">Aucun logement disponible.</p>
  {:else}
    <div
      class="grid grid-cols-[repeat(auto-fill,minmax(300px,1fr))] gap-6 justify-items-center"
    >
      {#each logements as logement (logement.id)}
        <LogementCard
          {...logement}
          on:supprimer={(e) => supprimerLogement(e.detail)}
          on:mettreAJour={(e) =>
            mettreAJourLogement(e.detail.id, e.detail.logement)}
        />
      {/each}
    </div>
  {/if}
</main>

<!--==================================LE FOOTER==================================-->

<footer
  class="bg-[#1E293B] border-t border-[#334155] text-[#CBD5E1] text-center py-6 mt-10"
>
  <p class="text-sm font-light">2026 - Réalisé par Lyna & Samy</p>
</footer>
