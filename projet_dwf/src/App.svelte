<script>
  import { onMount } from "svelte";
  import Router from "svelte-spa-router";
  import "./app.css";
  import Liste from "./pages/Liste.svelte";
  import Detail from "./components/Details.svelte";

  const routes = {
    "/": Liste,
    "/logements/:id": Detail,
  };
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
    <button class="text-[#0594D0] hover:text-[#047AAD] ml-2 text-lg">🔍</button>
  </div>
</header>

<!--==================================LE MAIN==================================-->

<main class="min-h-screen bg-[#0B1220] p-8 font-sans text-[#E5E7EB]">
  <Router {routes} />
</main>

<!--==================================LE FOOTER==================================-->

<footer
  class="bg-[#1E293B] border-t border-[#334155] text-[#CBD5E1] text-center py-6 mt-10"
>
  <p class="text-sm font-light">2026 - Réalisé par Lyna & Samy</p>
</footer>