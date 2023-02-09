<template>
  <main>
    <div>
      <table>
        <caption>
          Liste des produits
        </caption>
        <tr>
          <th>Nom</th>
          <th>Prix</th>
          <th>Stock</th>
          <th>Commandés</th>
        </tr>
        <!-- Si le tableau des produits est vide -->
        <tr v-if="!data.listeProduits">
          <td colspan="4">Veuillez patienter, chargement des produits...</td>
        </tr>
        <!-- Si le tableau des produits n'est pas vide -->
        <tr v-for="produit in data.listeProduits" :key="produit.reference">
          <td>{{ produit.nom }}</td>
          <td>{{ produit.prixUnitaire }}</td>
          <td>{{ produit.unitesEnStock }}</td>
          <td>{{ produit.unitesCommandees }}</td>
        </tr>
        <tr>
          <td>
            <button @click="chargerProduitsVoulus(data.links.first.href)">
              Premier
            </button>
          </td>
          <td>
            <button @click="chargerProduitsVoulus(data.links.prev.href)">
              Précédent
            </button>
          </td>
          <td>
            <button @click="chargerProduitsVoulus(data.links.next.href)">
              Suivant
            </button>
          </td>
          <td>
            <button @click="chargerProduitsVoulus(data.links.last.href)">
              Dernier
            </button>
          </td>
        </tr>
      </table>
    </div>
  </main>
</template>

<script setup>
import { reactive, onMounted } from "vue";
import { BACKEND, doAjaxRequest } from "../api";

// Pour réinitialiser le formuaire
const produitVide = {
  libelle: "",
  description: "",
};

let data = reactive({
  // La liste des produits affichée sous forme de table
  listeProduits: [],
  links: {},
  page: {},
});

function chargeProduits() {
  // Appel à l'API pour avoir la liste des produits
  // Trié par reference, descendant
  // Verbe HTTP GET par défaut
  doAjaxRequest(BACKEND + "/api/produits?size=5&sort=reference,asc")
    .then((json) => {
      data.listeProduits = json._embedded.produits;
      data.links = json._links;
      data.page = json.page;
    })
    .catch((error) => alert(error.message));
}

function chargerProduitsVoulus(href) {
  doAjaxRequest(href)
    .then((json) => {
      data.listeProduits = json._embedded.produits;
      data.links = json._links;
      data.page = json.page;
    })
    .catch((error) => alert(error.message));
}

// A l'affichage du composant, on affiche la liste
onMounted(chargeProduits);
</script>

<style scoped>
td,
th {
  border: 1px solid #ddd;
  padding: 8px;
}

th {
  padding-top: 12px;
  padding-bottom: 12px;
  text-align: left;
  background-color: #232623;
  color: rgb(255, 255, 255);
}
</style>
