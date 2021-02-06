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
            <!-- {{ abilities }} -->
            <div v-for="ability in abilities" :key="ability">{{ability.name}}</div>
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
  created() {
    const axios = require("axios").default;
    let self = this;
    const pokeAbs = [];
    const images = [];
    const pokemonsitos = async () => {
      try {
        const res = await axios.get(
          "https://pokeapi.co/api/v2/pokemon?limit=151"
        );
        // console.log(res.data.results[0].url);
        const urls = [];
        for (let i = 0; i < res.data.results.length; i++) {
          urls.push(res.data.results[i].url);
        }
        // https://stackoverflow.com/questions/44402079/how-to-make-multiple-axios-requests-using-a-dynamic-array-of-links-in-javascript/44402712
        axios.all(urls.map((url) => axios.get(url))).then(
          axios.spread(function (...res) {
            // console.log(res[0].data.sprites.front_default);
            for (let i = 0; i < res.length; i++) {
              pokeAbs.push(res[i].data.abilities);
              images.push(res[i].data.sprites.front_default);
            }
            self.pokeAbilities = [...pokeAbs];
            self.imagesArray = [...images];
            // pokeAbilities -> pokemon -> ability -> ability.name
            console.log(self.pokeAbilities[1][1].ability.name);
          })
        );

        //this.pokemones = res.data.results;
        // Para capitalizar la primera letra
        for (let i = 0; i < res.data.results.length; i++) {
          const pokemon =
            res.data.results[i].name.charAt(0).toUpperCase() +
            res.data.results[i].name.substring(1);
          this.pokemones.push(pokemon);
        }
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
