<template>
    <main>
        <div>
            <table>
                <caption> Liste des produits <p> Page : {{ data.page.number + 1 }} / {{ data.page.totalPages }} </p> </caption>
                <tr>
                    <th>Nom</th>
                    <th>Prix</th>
                    <th>Stock</th>
                    <th>Commandés</th>
                </tr>
                <!-- Si le tableau des catégories est vide -->
                <tr v-if="data.listeProduits.length === 0">
                    <td colspan="4">Veuillez patienter, chargement des produits...</td>
                </tr>
                <!-- Si le tableau des catégories n'est pas vide -->
                <tr v-for="produit in data.listeProduits" :key="produit.reference">
                    <td>{{ produit.nom }}</td>
                    <td>{{ produit.prixUnitaire }}</td>
                    <td>{{ produit.unitesEnStock }}</td>
                    <td>{{ produit.unitesCommandees }}</td>
                </tr>
                <tr>
                    <td>
                        <button @click="PremierePage">
                            ←
                            ←
                        </button>
                    </td>
                    <td>
                        <button @click="PagePrecedente">
                            ←
                        </button>
                    </td>
                    <td>
                        <button @click="PageSuivante">
                            →
                        </button>
                    </td>
                    <td>
                        <button @click="DernierePage">
                            →
                            →
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


let data = reactive({
    // La liste des produits affichée sous forme de table
    listeProduits: [],
    links: {},
    page: {}
});


function PageSuivante(){
    console.log("page suivante")
    doAjaxRequest (data.links.next.href)
    .then((json) => {
        data.listeProduits = json._embedded.produits;
        data.links = json._links;
        data.page = json.page;
    })
    .catch(showError);
}

function PagePrecedente(){
    console.log("Page precedente")
    doAjaxRequest(data.links.prev.href)
    .then((json) => {
        data.listeProduits = json._embedded.produits;
        data.links = json._links;
        data.page = json.page;
    })
    .catch(showError);
}

function PremierePage() {
    console.log("Premiere Page")
    doAjaxRequest(data.links.first.href)
    .then((json) => {
        data.listeProduits = json._embedded.produits;
        data.links = json._links;
        data.page = json.page;
    })
    .catch(showError);
}

function DernierePage() {
    console.log("Derniere Page")
    doAjaxRequest(data.links.last.href)
    .then((json) => {
        data.listeProduits = json._embedded.produits;
        data.links = json._links;
        data.page = json.page;
    })
    .catch(showError);
}

function showError(error) {
    console.log("Erreur : status %d", error.status)
    console.log(error.body);
    alert(error.message);
}

function chargeProduits() {
    // Appel à l'API pour avoir la liste des catégories
    // Trié par code, descendant
    // Verbe HTTP GET par défaut
    doAjaxRequest(BACKEND + "/api/produits?page=0&size=5")
        .then((json) => {
            data.listeProduits = json._embedded.produits;
            data.links = json._links;
            console.log(links)
        })
        .catch(showError);
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