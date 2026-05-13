<script>
 import { createEventDispatcher } from "svelte";
 const dispatch = createEventDispatcher();

 let nom = "";
 let ville = "";
 let prix = "";
 let imageUrl = "";
 let description = "";
 let capacite = "";
 let erreur = "";

 export let formulaireVisible = false;

 async function ajouterLogement(event) {
  event.preventDefault();
  if (!nom || !ville || !prix || !imageUrl || !description || !capacite) return;

  erreur = "";

  const nouveauLogement = {
   nom,
   ville,
   prix: parseFloat(prix),
   imageUrl,
   description,
   capacite: parseInt(capacite),
   proprietaireId: "69f8b01a50b9a786a524fd2d",
  };

  const response = await fetch("/api/logements", {
   method: "POST",
   headers: { "Content-Type": "application/json" },
   body: JSON.stringify(nouveauLogement),
  });

  if (response.ok) {
   dispatch("ajoute"); // prévient le parent de recharger
   nom = "";
   ville = "";
   prix = "";
   imageUrl = "";
   description = "";
   capacite = "";
   formulaireVisible = false;
  } else {
   erreur = "Erreur lors de l'ajout du logement";
  }
 }
</script>

<div class="flex gap-3 mb-6">
 <button
  class="bg-[#0594D0] hover:bg-[#047AAD] text-white px-5 py-2 rounded-lg transition shadow-md"
  on:click={() => (formulaireVisible = true)}
 >
  Ajouter logement
 </button>
</div>

{#if formulaireVisible}
 <form
  on:submit={ajouterLogement}
  class="bg-white rounded-xl shadow-md p-6 mb-8 w-full flex flex-col gap-3"
 >
  <h2 class="text-blue-500 text-xl font-bold">Nouveau logement</h2>

  <input
   class="w-full border border-gray-300 rounded-lg px-3 py-2 focus:outline-none focus:border-blue-500"
   bind:value={nom}
   name="nom"
   placeholder="Nom du logement"
  />

  <input
   class="w-full border border-gray-300 rounded-lg px-3 py-2 focus:outline-none focus:border-blue-500"
   bind:value={ville}
   name="ville"
   placeholder="Ville"
  />

  <input
   class="w-full border border-gray-300 rounded-lg px-3 py-2 focus:outline-none focus:border-blue-500"
   bind:value={prix}
   name="prix"
   placeholder="Prix (€)"
   type="number"
  />

  <input
   class="w-full border border-gray-300 rounded-lg px-3 py-2 focus:outline-none focus:border-blue-500"
   bind:value={capacite}
   name="capacite"
   placeholder="Capacité"
   type="number"
  />

  <input
   class="w-full border border-gray-300 rounded-lg px-3 py-2 focus:outline-none focus:border-blue-500"
   bind:value={imageUrl}
   name="imageUrl"
   placeholder="URL de l'image"
  />

  <textarea
   class="w-full border border-gray-300 rounded-lg px-3 py-2 focus:outline-none focus:border-blue-500 resize-y min-h-20"
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
