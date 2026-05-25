<script>
  import { onMount } from "svelte";
  import Reservation from "./Reservation.svelte";
  export let params;

  let logement = null;
  let reservations = [];
  let reservationVisible = false;

 onMount(async () => {
  const response = await fetch(`/api/logements/${params.id}`);
  logement = await response.json();

  const resResa = await fetch(`/api/reservations`);
  const toutesLesResa = await resResa.json();

  reservations = toutesLesResa.filter(r => r.logementId === params.id);
  console.log("Réservations filtrées:", reservations);
});
</script>

{#if logement}
  <img class="w-full h-64 object-cover" src={logement.imageUrl} alt={logement.nom} />
  <div class="p-5 flex flex-col gap-3">
    <h1 class="text-white text-2xl font-bold">{logement.nom}</h1>
    <p class="text-[#0594D0]">{logement.ville}</p>
    <p class="text-[#94A3B8] text-sm leading-relaxed">{logement.description}</p>
    <span class="text-xs bg-[#0594D0]/10 text-[#0594D0] border border-[#0594D0]/20 px-3 py-1 rounded-full w-fit">
      {logement.capacite} personnes
    </span>
    <p class="text-white font-black text-xl">{logement.prix} € <span class="text-[#64748B] text-sm font-normal">/ nuit</span></p>
  </div>

  <button
    on:click={() => (reservationVisible = true)}
    class="bg-sky-500 hover:bg-sky-600 text-white px-5 py-2 rounded-lg font-bold transition"
  >
    📅 Réserver
  </button>

  {#if reservationVisible}
    <Reservation
      logementId={logement.id}
      {reservations}
      on:fermer={() => (reservationVisible = false)}
    />
  {/if}

{:else}
  <p class="text-[#94A3B8] p-5">Chargement...</p>
{/if}