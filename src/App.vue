<template>
  <div id="page">
    <div id="container">
      <label for="pays">Pays</label>
      <input @click.stop="showModal" id="pays" v-model="search" />
      <div v-if="showMenu" id="liste">
        <div v-for="(country, index) in countryFilter" :key="index">
          <div @click="copyCountry(country.translations.fr)">
            <img width="25px" :src="country.flag" />
            <span v-html="countryBold(country.translations.fr)"></span>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from "axios";
export default {
  name: "app",
  data() {
    return { showMenu: false, info: [], search: "" };
  },
  mounted() 
  // Evenement pour cacher le menu déroulant avec un click sur la page (l'élément #page prend 100% de l'écran cf. CSS),
  // l'évenement ne se propage pas sur la div#container qui contient le label l'input et le menu lui même
    {
    document.addEventListener("click", () => {
      this.showMenu = false;
      this.search = "";
    });
    document.querySelector("#container").addEventListener("click", stop);
    function stop(e) {
      e.stopPropagation();
    }
    // Axios pour récuperer les data du REST
    axios
      .get("https://restcountries.eu/rest/v2/all")
      .then(response => (this.info = response.data));
  },
  methods: {
    // Display le menu déroulant
    showModal() {
      this.showMenu = true;
    },
    // Copie le pays au click sur la div qui contient l'image et le nom du pays
    copyCountry(name) {
      this.search = name;
    },
    // Mise en forme du texte : argument name, le place dans une balise <strong> s'il correspond à search 
    countryBold(name) {
      if (this.search !== "") {
        return name.replace(new RegExp(this.search, "i"), match => {
          return "<strong>" + match + "</strong>";
        });
      }
      return name;
    }
  },
  computed: {
    countryFilter() {
      // si search non vide
      if (this.search !== "") {
        // Pour tous les pays dans infos, je les retourne
        return this.info.filter(country => {
          // si la traduction française existe
          if (country.translations.fr != null) {
            // et la recherche dans search correspond, (taille et index)
            return (
              country.translations.fr
                .toLowerCase()
                .indexOf(this.search.toLowerCase()) < this.search.length &&
              country.translations.fr
                .toLowerCase()
                .indexOf(this.search.toLowerCase()) == 0
            );
          }
        });
      }
      return this.info;
    }
  }
};
</script>

<style>
@import "./css/styles.css";
</style>
