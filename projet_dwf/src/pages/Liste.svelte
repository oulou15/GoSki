<script>
  import { onMount } from "svelte";
  import LogementCard from "../components/LogementCard.svelte";
  import Ajouter from "../components/Ajouter.svelte";

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

  // Suppression
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
</script>

<!-- Confirmation de suppression -->
{#if confirmerVisible}
  <div class="fixed w-full h-full bg-black/50 flex items-center justify-center z-50">
    <div class="bg-white rounded-xl p-6 text-center shadow-xl mx-4">
      <p class="mb-4 text-gray-800">Êtes-vous sûr de vouloir supprimer ce logement ?</p>
      <div class="flex gap-3 justify-center">
        <button class="bg-red-500 text-white px-4 py-2 rounded-lg" on:click={confirmer}>Oui</button>
        <button class="bg-gray-300 px-4 py-2 rounded-lg" on:click={() => (confirmerVisible = false)}>Non</button>
      </div>
    </div>
  </div>
{/if}

<!-- Ajout de logement -->
<Ajouter on:ajoute={getLogements} />

<!-- Affichage des logements -->
{#if logements.length === 0}
  <p class="text-[#9CA3AF] mt-4">Aucun logement disponible.</p>
{:else}
  <div class="grid grid-cols-[repeat(auto-fill,minmax(300px,1fr))] gap-6 justify-items-center">
    {#each logements as logement (logement.id)}
      <LogementCard
        {...logement}
        on:supprimer={(e) => supprimerLogement(e.detail)}
        on:modifie={getLogements}
      />
    {/each}
  </div>
{/if}