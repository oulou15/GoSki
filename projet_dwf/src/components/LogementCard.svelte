<script>
  import { createEventDispatcher } from "svelte";
  import Modifier from "./Modifier.svelte";

  export let id;
  export let nom;
  export let ville;
  export let prix;
  export let imageUrl;
  export let description;
  export let capacite;

  const dispatch = createEventDispatcher();
</script>

<div
  class="carte w-full h-full rounded-2xl overflow-hidden bg-white border border-sky-100 p-5 hover:scale-105 hover:shadow-xl"
>
  <img class="w-full h-48 object-cover rounded-t-xl" src={imageUrl} alt={nom} />
  <div class="infos bg-gradient-to-b from-white to-sky-50 rounded-b-xl p-5">
    <h2 class="text-xl text-slate-900 font-extrabold mb-1">{nom}</h2>
    <p
      class="text-gray-600 font-bold flex items-center gap-1 text-sm uppercase"
    >
      {ville}
    </p>

    <div class="flex justify-between items-center border-t border-sky-100 pt-4">
      <p class="text-sky-600 font-black text-xl">
        {prix} € <span class="text-sm font-normal text-slate-400">/ nuit</span>
      </p>
    </div>

    <div class="boutons flex flex-col gap-2 mt-3">
      <a
        href="#/logements/{id}"
        class="w-full bg-sky-500 hover:bg-sky-600 text-white py-2 rounded-lg font-bold text-sm transition-colors text-center block"
      >
        Voir les détails
      </a>

      <Modifier {id} on:modifie={() => dispatch("modifie")} />

      <button
        on:click={() => dispatch("supprimer", id)}
        class="w-full bg-red-500 hover:bg-red-600 text-white py-2 rounded-lg font-bold text-sm transition-colors"
      >
        Supprimer
      </button>
    </div>
  </div>
</div>
