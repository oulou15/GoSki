<script>
 import { onMount } from "svelte";
 export let id;

 let nom = "";
 let ville = "";
 let prix = "";
 let imageUrl = "";
 let description = "";
 let capacite = "";

 let erreur = "";

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
</script>
