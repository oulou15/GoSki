<script>
 import { createEventDispatcher } from "svelte";
 const dispatch = createEventDispatcher();

 export let id;
 export let formulaireVisible = false;

 let nom = "";
 let ville = "";
 let prix = "";
 let imageUrl = "";
 let description = "";
 let capacite = "";
 let erreur = "";

 import { onMount } from "svelte";

 onMount(async () => {
  try {
   const res = await fetch(`/api/logements/${id}`);
   const data = await res.json();
   nom = data.nom;
   ville = data.ville;
   prix = data.prix;
   imageUrl = data.imageUrl;
   description = data.description;
   capacite = data.capacite;
  } catch (err) {
   console.error(err);
   erreur = "Impossible de charger le logement";
  }
 });

 async function modifierLogement(event) {
  event.preventDefault();
  if (!nom || !ville || !prix || !imageUrl || !description || !capacite) return;

  erreur = "";

  const logementMisAJour = {
   nom,
   ville,
   prix: parseFloat(prix),
   imageUrl,
   description,
   capacite: parseInt(capacite),
  };

  const response = await fetch(`/api/logements/${id}`, {
   method: "PUT",
   headers: { "Content-Type": "application/json" },
   body: JSON.stringify(logementMisAJour),
  });

  if (response.ok) {
   dispatch("modifie");
   formulaireVisible = false;
  } else {
   erreur = "Erreur lors de la modification du logement";
  }
 }
</script>

<div class="flex gap-3 mb-6">
 <button
  class="bg-amber-400 hover:bg-amber-500 text-[#0B1220] px-5 py-2 rounded-lg transition shadow-md font-bold"
  on:click={() => (formulaireVisible = true)}
 >
  ✏️ Modifier
 </button>
</div>

{#if formulaireVisible}
 <form
  on:submit={modifierLogement}
  class="bg-white rounded-xl shadow-md p-6 mb-8 w-full flex flex-col gap-3"
 >
  <h2 class="text-amber-500 text-xl font-bold">Modifier le logement</h2>

  <input
   class="w-full border border-gray-300 rounded-lg px-3 py-2 focus:outline-none focus:border-amber-400"
   bind:value={nom}
   name="nom"
   placeholder="Nom du logement"
  />

  <input
   class="w-full border border-gray-300 rounded-lg px-3 py-2 focus:outline-none focus:border-amber-400"
   bind:value={ville}
   name="ville"
   placeholder="Ville"
  />

  <input
   class="w-full border border-gray-300 rounded-lg px-3 py-2 focus:outline-none focus:border-amber-400"
   bind:value={prix}
   name="prix"
   placeholder="Prix (€)"
   type="number"
  />

  <input
   class="w-full border border-gray-300 rounded-lg px-3 py-2 focus:outline-none focus:border-amber-400"
   bind:value={capacite}
   name="capacite"
   placeholder="Capacité"
   type="number"
  />

  <input
   class="w-full border border-gray-300 rounded-lg px-3 py-2 focus:outline-none focus:border-amber-400"
   bind:value={imageUrl}
   name="imageUrl"
   placeholder="URL de l'image"
  />

  <textarea
   class="w-full border border-gray-300 rounded-lg px-3 py-2 focus:outline-none focus:border-amber-400 resize-y min-h-20"
   bind:value={description}
   name="description"
   placeholder="Description"
  />

  <div class="flex gap-3">
   {#if erreur}
    <p class="text-red-500 text-sm">{erreur}</p>
   {/if}

   <!-- Boutons de validation et d'annulation -->

   <button
    type="submit"
    class="bg-green-500 hover:bg-green-600 text-white px-5 py-2 rounded-lg cursor-pointer transition"
   >
    Valider
   </button>
   <button
    type="button"
    class="bg-red-500 hover:bg-red-600 text-white px-5 py-2 rounded-lg cursor-pointer transition"
    on:click={() => (formulaireVisible = false)}
   >
    Annuler
   </button>
  </div>
 </form>
{/if}
