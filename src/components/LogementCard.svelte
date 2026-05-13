<script>
  import { createEventDispatcher } from "svelte";

  export let id;
  export let nom;
  export let ville;
  export let prix;
  export let imageUrl;
  export let description;
  export let capacite;

  const dispatch = createEventDispatcher();
  let showDetails = false;
  let FormulaireModifVisible = false;

  let editNom = nom;
  let editVille = ville;
  let editPrix = prix;
  let editImageUrl = imageUrl;
  let editDescription = description;
  let editCapacite = capacite;

  function soumettreMaj(event) {
    event.preventDefault();
    dispatch("mettreAJour", {
      id,
      logement: {
        nom: editNom,
        ville: editVille,
        prix: parseFloat(editPrix),
        imageUrl: editImageUrl,
        description: editDescription,
        capacite: parseInt(editCapacite),
      },
    });
    FormulaireModifVisible = false;
  }
</script>

<div
  class="w-full rounded-2xl overflow-hidden bg-[#162032] border border-[#2D3F55] hover:border-[#0594D0] transition-all duration-300"
>
  <img class="w-full h-52 object-cover" src={imageUrl} alt={nom} />

  <div class="p-5 flex flex-col gap-3">
    <div>
      <h2 class="text-white text-xl font-bold">{nom}</h2>
      <p class="text-[#0594D0] text-sm font-medium mt-1">{ville}</p>
    </div>

    {#if showDetails}
      <p class="text-[#94A3B8] text-sm leading-relaxed">{description}</p>
      <span
        class="text-xs bg-[#0594D0]/10 text-[#0594D0] border border-[#0594D0]/20 px-3 py-1 rounded-full w-fit"
      >
        {capacite} personnes
      </span>
    {/if}

    <div
      class="flex justify-between items-center pt-3 border-t border-[#2D3F55]"
    >
      <p class="text-white font-black text-xl">
        {prix} €
        <span class="text-[#64748B] text-sm font-normal">/ nuit</span>
      </p>
      <button
        on:click={() => (showDetails = !showDetails)}
        class="text-[#0594D0] text-sm font-semibold hover:underline transition"
      >
        {showDetails ? "Masquer" : "Voir plus"}
      </button>
    </div>

    {#if FormulaireModifVisible}
      <form
        on:submit={soumettreMaj}
        class="flex flex-col gap-3 mt-2 pt-4 border-t border-[#2D3F55]"
      >
        <h3 class="text-white font-bold text-base">Modifier le logement</h3>

        <input
          class="w-full bg-[#0B1220] border border-[#2D3F55] text-white rounded-lg px-3 py-2 focus:outline-none focus:border-[#0594D0] placeholder-[#64748B] text-sm"
          bind:value={editNom}
          name="nom"
          placeholder="Nom du logement"
        />

        <input
          class="w-full bg-[#0B1220] border border-[#2D3F55] text-white rounded-lg px-3 py-2 focus:outline-none focus:border-[#0594D0] placeholder-[#64748B] text-sm"
          bind:value={editVille}
          name="ville"
          placeholder="Ville"
        />

        <input
          class="w-full bg-[#0B1220] border border-[#2D3F55] text-white rounded-lg px-3 py-2 focus:outline-none focus:border-[#0594D0] placeholder-[#64748B] text-sm"
          bind:value={editPrix}
          name="prix"
          placeholder="Prix (euros)"
          type="number"
        />

        <input
          class="w-full bg-[#0B1220] border border-[#2D3F55] text-white rounded-lg px-3 py-2 focus:outline-none focus:border-[#0594D0] placeholder-[#64748B] text-sm"
          bind:value={editCapacite}
          name="capacite"
          placeholder="Capacité"
          type="number"
        />

        <input
          class="w-full bg-[#0B1220] border border-[#2D3F55] text-white rounded-lg px-3 py-2 focus:outline-none focus:border-[#0594D0] placeholder-[#64748B] text-sm"
          bind:value={editImageUrl}
          name="imageUrl"
          placeholder="URL de l'image"
        />

        <textarea
          class="w-full bg-[#0B1220] border border-[#2D3F55] text-white rounded-lg px-3 py-2 focus:outline-none focus:border-[#0594D0] placeholder-[#64748B] text-sm resize-y min-h-20"
          bind:value={editDescription}
          name="description"
          placeholder="Description"
        />

        <div class="flex gap-2">
          <button
            type="submit"
            class="flex-1 bg-emerald-500 hover:bg-emerald-600 text-white py-2 rounded-lg text-sm font-bold transition"
          >
            Valider
          </button>
        </div>
      </form>
    {/if}

    <div class="flex gap-2 mt-1">
      <button
        on:click={() => {
          FormulaireModifVisible = !FormulaireModifVisible;
          showDetails = false;
        }}
        class="flex-1 bg-amber-400 hover:bg-amber-500 text-[#0B1220] py-2 rounded-lg text-sm font-bold transition"
      >
        {FormulaireModifVisible ? " Annuler la modification" : " Modifier"}
      </button>

      <button
        on:click={() => dispatch("supprimer", id)}
        class="flex-1 bg-red-500 hover:bg-red-600 text-white py-2 rounded-lg text-sm font-bold transition"
      >
        Supprimer
      </button>
    </div>
  </div>
</div>
