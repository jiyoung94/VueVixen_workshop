<template>
  <v-container grid-list-md fluid>
    <v-layout wrap>
      <v-flex xs12 sm4 md3 v-for="pet in dogs" :key="pet.breed">
        <app-dog :dog="pet" @addToFavorites="addToFavorites"></app-dog>
      </v-flex>
    </v-layout>
  </v-container>
</template>

<script>
import { Dogs } from "../data/dogs";
import Dog from "../components/Dog.vue";
import axios from "axios";
import { mapActions } from "vuex";
axios.defaults.baseURL = "https://dog.ceo/api";

export default {
  components: {
    appDog: Dog
  },
  methods: {
    ...mapActions(["addToFavorites"])
  },
  data() {
    return {
      dogs: Dogs
    };
  },

  created() {
    this.dogs.forEach(dog => {
      dog.img = "";
    });
    const linksArray = this.dogs.map(
      dog => "/breed/" + dog.breed + "/images/random"
    );
    axios.all(linksArray.map(link => axios.get(link))).then(
      axios.spread((...res) => {
        this.dogs.forEach((dog, index) => {
          dog.img = res[index].data.message;
        });
      })
    );
    axios
      .get("/breed/husky/images/random")
      .then(response => {
        const husky = this.dogs.find(dog => dog.breed === "husky");
        console.log(husky);
        husky.img = response.data.message;
      })
      .catch(error => {
        console.log(error);
      });
  }
};
</script>

