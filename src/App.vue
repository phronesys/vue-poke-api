<template>
  <div class="header"></div>
  <div class="invetory">
    <h1 class="inventory__header">
      Has click en un pokemon para añadirlo a la lista
    </h1>
    <ul class="inventory__list-box">
      <li
        v-for="poke in lista"
        :key="poke"
        class="inventory__list"
        @click="remove(poke)"
      >
        {{ poke }}
      </li>
    </ul>
  </div>
  <div class="main">
    <!-- <div>{{images}}</div> -->

    <ul class="container">
      <li
        class="item"
        v-for="(pokemon, index) in pokemones"
        :key="index"
        @click="selectPokemon(pokemon)"
      >
        <button class="nes-btn is-succes btn__add">+</button>
        <p>
          {{ pokemon }}
        </p>

        <!-- <p>{{ imagesArray[1] }}</p> -->
        <img :src="imagesArray[index]" :alt="pokemon" class="item__img" />
        <ul class="abilities">
          <li v-for="abilities in pokeAbilities[index]" :key="abilities">
            <!-- {{ abilities }} -->
            <div
              v-for="ability in abilities"
              :key="ability"
              class="abilities__text"
            >
              {{ ability.name }}
            </div>
          </li>
        </ul>
        <div class="buttons"></div>
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
      lista: [],
    };
  },
  methods: {
    selectPokemon(pokemon) {
      if (!this.lista.includes(pokemon)) {
        console.log("added: " + pokemon);
        this.lista.push(pokemon);
      } else {
        console.log("not added:" + pokemon);
      }
    },
    remove(poke) {
      this.lista = this.lista.filter((r) => r !== poke);
    },
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
            // console.log(self.pokeAbilities[1][1].ability.name);
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

<style lang="scss">
#app {
  font-family: "Press Start 2P", cursive;
  font-size: 70%;
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
  background-position: center;
  background-size: cover;
}
.container {
  // display: flex;
  // flex-wrap: wrap;
  display: inline-block;
  width: 90%;
  margin: 1rem;
  padding: 1rem;
}
.btn__add {
  position: relative;
  top: -1.5rem;

}
.item {
  display: inline-block;
  vertical-align: top;
  width: 200px;
  height: 300px;
  list-style: none;
  background-color: white;
  border-radius: 10px;
  box-shadow: 0 1rem 2rem rgba(0, 0, 0, 0.6);
  margin: 1rem 1rem;
  padding: 2rem;
  transition: all 0.6s;
  &:hover {
    transform: scale(1.2);
    box-shadow: 0.2rem 1rem 3rem rgba(0, 0, 0, 0.6);
  }
  &:hover &__img {
    transform: scale(2);
    filter: none;
  }
  &__img {
    width: 100px;
    padding: 1rem;
    filter: grayscale(100%) blur(1px) drop-shadow(5px 5px 5px #000);
  }
}
.abilities {
  list-style: none;
  padding: 0;
  margin: 0;
  &__text {
    &:not(&:last-child) {
      margin-bottom: 0.1rem;
    }
  }
}
.inventory {
  height: 100rem;
  margin: 1rem auto;
  &__header {
    color: #f5d536;
    background-color: #4b50a973;
    padding: 0.6rem;
    border-radius: 5px;
    width: fit-content;
    position: relative;
    top: 50%;
    left: 50%;
    transform: translate(-50%);
  }
  &__list-box {
    padding-left: 1rem;
  }
  &__list {
    display: inline-block;
    background-color: white;
    list-style: none;
    list-style-position: inside;

    border-radius: 10px;
    padding: 1rem;
    width: 5rem;
    margin-bottom: 1rem;
    &:not(&:last-child) {
      margin-right: 1rem;
    }
  }
}
@import "../node_modules/nes.css/css/nes.css";
@import url("https://fonts.googleapis.com/css2?family=Press+Start+2P&display=swap");
</style>
