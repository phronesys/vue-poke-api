<template>
  <div class="header"></div>
  <div class="main">
    <!-- <div>{{images}}</div> -->

    <ul class="container">
      <li class="item" v-for="(pokemon, index) in pokemones" :key="index">
        <p>
          {{ pokemon }}
        </p>
        <!-- <p>{{ imagesArray[1] }}</p> -->
        <img :src="imagesArray[index]" alt="" />
        <ul class="abilities">
          <li v-for="abilities in pokeAbilities[index]" :key="abilities">
            {{ abilities }}
          </li>
        </ul>
      </li>
    </ul>
  </div>
  <div class="footer">the footer</div>
</template>

<script>
export default {
  data() {
    return {
      pokemones: [],
      imagesArray: [],
      pokeAbilities: [],
    };
  },
  methods: {
    saveLocal() {},
  },
  created() {
    const axios = require("axios").default;
    const pokemonsitos = async () => {
      try {
        const res = await axios.get(
          "https://pokeapi.co/api/v2/pokemon?limit=151"
        );
        // console.log(res.data.results);
        //this.pokemones = res.data.results;
        // Para capitalizar la primera letra
        for (let i = 0; i < res.data.results.length; i++) {
          const pokemon =
            res.data.results[i].name.charAt(0).toUpperCase() +
            res.data.results[i].name.substring(1);
          this.pokemones.push(pokemon);
        }
        // cada pokemon tiene que hacer HTTP request
        // para asi obtener su imagen y stats
        for (const pokemon of res.data.results) {
          // console.log(pokemon.url);
          const pokemonImages = async () => {
            try {
              const res = await axios.get(pokemon.url);
              this.imagesArray.push(res.data.sprites.front_default);
              // console.log(res.data.abilities[0].ability.name);

              const array = [];
              for (let i = 0; i < res.data.abilities.length; i++) {
                // console.log(res.data.abilities[i].ability.name);
                array.push(res.data.abilities[i].ability.name);
              }
              // console.log(array);
              this.pokeAbilities.push(array);
            } catch (error) {
              console.error(error);
            }
          };
          pokemonImages();
        }
        const pokes = JSON.stringify(this.pokemones);
        window.localStorage.setItem("pokemones", pokes);
        console.log(JSON.parse(window.localStorage.getItem("pokemones")));
      } catch (error) {
        console.error(error);
      }
    };
    pokemonsitos();
  },
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  background-color: red;
}
.header {
  width: 100%;
  height: 0;
  margin-bottom: 1rem;
  padding: 13rem 0;
  background-image: url("./assets/pokeapi.png");
  background-position: top;
  background-size: cover;
}
.container {
  display: flex;
  flex-wrap: wrap;
  margin: -0.5rem;
  padding: 5rem;
}
.item {
  width: 100px;
  list-style: none;
  background-color: white;
  border-radius: 10px;
  box-shadow: 0 1rem 2rem rgba(0, 0, 0, 0.6);
  margin: 0.4rem;
  padding: 1rem;
}
.abilities {
  list-style: none;
  padding: 0;
  margin: 0;
}
</style>
