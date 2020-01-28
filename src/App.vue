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
  mounted() {
    document.addEventListener("click", () => {
      this.showMenu = false;
      this.search = "";
    });
    document.querySelector("#container").addEventListener("click", stop);
    function stop(e) {
      e.stopPropagation();
    }
    axios
      .get("https://restcountries.eu/rest/v2/all")
      .then(response => (this.info = response.data));
  },
  methods: {
    showModal() {
      this.showMenu = true;
    },
    copyCountry(name) {
      this.search = name;
    },
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
      if (this.search !== "") {
        return this.info.filter(country => {
          if (country.translations.fr != null) {
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
