<template>
  <div class="header">the header</div>
  <div class="main">
    <ul class="container">
      <li class="item" v-for="pokemon in pokemones" :key="pokemon">
        {{ pokemon.url }}
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
    };
  },
  created() {
    const axios = require("axios").default;
    const pokemonsitos = async () => {
      try {
        const res = await axios.get(
          "https://pokeapi.co/api/v2/pokemon?limit=151"
        );
        console.log(res.data.results);
        this.pokemones = res.data.results;
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
  margin-top: 60px;
}
.container {
  display: flex;
  flex-wrap: wrap;
  margin: -0.5rem;
}
.item {
  width: 100px;
  list-style: none;
  border: 1px solid black;
  margin: 0.5rem;
  padding: 5rem;
}
</style>
