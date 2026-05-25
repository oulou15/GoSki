<script>
  import { createEventDispatcher } from "svelte";

  export let logementId;
  export let reservations = [];

  const dispatch = createEventDispatcher();

  let dateArrivee = "";
  let dateDepart = "";
  let message = "";
  let succes = false;
  let chargement = false;

  $: console.log("reservations reçues:", reservations, "length:", reservations.length);

  function verifierDisponibilite(arrivee, depart) {
    if (!arrivee || !depart) return null;
    const debut = new Date(arrivee);
    const fin = new Date(depart);

    for (const r of reservations) {
      const rDebut = new Date(r.dateArrivee);
      const rFin = new Date(r.dateDepart);
      if (debut < rFin && fin > rDebut) return false;
    }
    return true;
  }

  $: datesValides = dateArrivee && dateDepart && dateDepart > dateArrivee;
  $: disponible = datesValides ? verifierDisponibilite(dateArrivee, dateDepart) : null;

  async function reserver() {
    if (!datesValides) {
      message = "Veuillez remplir les deux dates.";
      succes = false;
      return;
    }
    if (!disponible) {
      message = "Ce logement est indisponible pour ces dates.";
      succes = false;
      return;
    }

    chargement = true;
    message = "";

    try {
      const response = await fetch("/api/reservations", {
        method: "POST",
        headers: { "Content-Type": "application/json" },
        body: JSON.stringify({
          logementId,
          dateArrivee,
          dateDepart,
          locataireId: "69f8b01a50b9a786a524fd2d",
        }),
      });

      if (response.ok) {
        succes = true;
        message = "Réservation effectuée avec succès !";
        dateArrivee = "";
        dateDepart = "";
      } else {
        succes = false;
        message = "Ce logement est indisponible pour ces dates.";
      }
    } catch (err) {
      succes = false;
      message = "Une erreur est survenue, veuillez réessayer.";
    } finally {
      chargement = false;
    }
  }
</script>


<div class="fixed inset-0 bg-black/50 flex items-center justify-center z-50">
  <div class="bg-white rounded-2xl shadow-2xl p-8 w-full max-w-md mx-4 flex flex-col gap-4">

    <div class="flex justify-between items-center">
      <h2 class="text-xl font-bold text-slate-800">Réserver ce logement</h2>
      <button on:click={() => dispatch("fermer")} class="text-gray-400 hover:text-gray-600 text-2xl font-bold leading-none">×</button>
    </div>

    {#if reservations.length > 0}
      <div class="bg-red-50 border border-red-200 rounded-lg px-3 py-2">
        <p class="text-sm font-semibold text-red-600 mb-1">Dates indisponibles :</p>
        {#each reservations as r}
          <p class="text-xs text-red-500">
            Du {new Date(r.dateArrivee).toLocaleDateString("fr-FR")}
            au {new Date(r.dateDepart).toLocaleDateString("fr-FR")}
          </p>
        {/each}
      </div>
    {:else}
      <p class="text-sm text-green-600 bg-green-50 border border-green-200 rounded-lg px-3 py-2">
        ✅ Aucune réservation en cours, toutes les dates sont libres !
      </p>
    {/if}

    <div class="flex flex-col gap-2">
      <label class="text-sm font-semibold text-slate-600">Date d'arrivée</label>
      <input type="date" bind:value={dateArrivee}
        class="border border-gray-300 rounded-lg px-3 py-2 focus:outline-none focus:border-sky-400" />
    </div>

    <div class="flex flex-col gap-2">
      <label class="text-sm font-semibold text-slate-600">Date de départ</label>
      <input type="date" bind:value={dateDepart}
        class="border border-gray-300 rounded-lg px-3 py-2 focus:outline-none focus:border-sky-400" />
    </div>

    {#if datesValides && !message}
      {#if disponible}
        <p class="text-sm font-medium text-green-600 bg-green-50 border border-green-200 rounded-lg px-3 py-2">
          Ces dates sont disponibles !
        </p>
      {:else}
        <p class="text-sm font-medium text-red-500 bg-red-50 border border-red-200 rounded-lg px-3 py-2">
          Ces dates chevauchent une réservation existante.
        </p>
      {/if}
    {/if}

    {#if message}
      <p class={`text-sm font-medium px-3 py-2 rounded-lg border ${succes ? "text-green-600 bg-green-50 border-green-200" : "text-red-500 bg-red-50 border-red-200"}`}>
        {message}
      </p>
    {/if}

    <div class="flex gap-3">
      <button
        on:click={reserver}
        disabled={chargement || !disponible}
        class="flex-1 bg-sky-500 hover:bg-sky-600 disabled:opacity-50 disabled:cursor-not-allowed text-white py-2 rounded-lg font-bold transition"
      >
        {chargement ? "Envoi..." : "Confirmer"}
      </button>
      <button on:click={() => dispatch("fermer")}
        class="flex-1 bg-gray-200 hover:bg-gray-300 text-slate-700 py-2 rounded-lg font-bold transition">
        Annuler
      </button>
    </div>

  </div>
</div>