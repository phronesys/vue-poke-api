<template>
  <div class="header" id="the-header"></div>
  <a type="button" class="nes-btn is-primary go-up" href="#the-header">up!</a>
  <div class="invetory">
    <h1 class="inventory__header">¿Cuál es ese pokémon?</h1>
    <ul class="inventory__list-box"></ul>
  </div>
  <div class="main">
    <h3 class="loading" v-if="isLoading">Loading...</h3>
    <ul class="container" v-else>
      <li
        class="item nes-container"
        v-for="(pokemon, index) in pokemones"
        :key="index"
        @click="logPoke(pokemon)"
      >
        <form
          class="nes-field"
          v-if="!checkList(index)"
          @submit.prevent="checkInput(pokemon, index)"
        >
          <label :for="index"></label>
          <input
            type="text"
            :id="index"
            class="nes-input item__input"
            v-model="adivinando[index]"
          />
        </form>
        <p v-else>
          {{ pokemon }}
        </p>

        <!-- <p>{{ imagesArray[1] }}</p> -->
        <img :src="imagesArray[index]" :alt="pokemon" :class="image(index)" />
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
  <div class="footer">
    <section class="icon-list">
      <p>This is powered by</p>
      <!-- github -->
      <a href="https://nostalgic-css.github.io/NES.css/" target="_blank"
        ><i class="nes-icon github is-large"></i
      ></a>
      <p>NES.css</p>
    </section>
  </div>
</template>

<script>
export default {
  data() {
    return {
      pokemones: [],
      imagesArray: [],
      pokeAbilities: [],
      lista: [],
      adivinando: new Array(150).fill(""),
      boolIndex: new Array(150).fill(false),
      counter: 0,
      isLoading: true
    };
  },
  computed: {},
  methods: {
    image(index) {
      return this.boolIndex[index] ? "item__img--show" : "item__img";
    },
    checkList(index) {
      return this.boolIndex[index];
    },
    checkInput(pokemon, index) {
      if (
        pokemon.toLowerCase() === this.adivinando[index] ||
        pokemon == this.adivinando[index]
      ) {
        this.counter++;
        this.boolIndex[index] = true;
        return true;
      }
    },
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
    logPoke(pokemon) {
      console.log(pokemon);
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
    pokemonsitos().then(() => {
      this.isLoading = false;
      
    });
  },
};
</script>

<style lang="scss">
html {
  scroll-behavior: smooth;
}
#app {
  font-family: "Press Start 2P", cursive;
  font-size: 70%;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  background-image: linear-gradient(
      to right,
      #0e0e0e 20%,
      transparent 60%,
      #0e0e0e 100%
    ),
    url("./assets/wallpaper.png");
}
.header {
  width: 100%;
  height: 0;
  margin-bottom: 1rem;
  padding: 13rem 0;
  background-image: url("./assets/pokeapi.png");
  background-position: center;
  background-size: cover;
  @media (max-width: 700px) {
    background-size: 100%;
    background-position: top;
    background-repeat: no-repeat;
    padding: 6rem 0;
  }
}
.container {
  // display: flex;
  // flex-wrap: wrap;
  display: inline-block;
  width: 90%;
  margin: 1rem;
  padding: 1rem;
}
// .btn__add {
//   position: relative;
//   top: -1.5rem;
// }
.item {
  display: inline-block;
  vertical-align: top;
  width: 200px;
  height: 320px;
  list-style: none;
  background-color: white;
  border-radius: 10px;
  box-shadow: 0 1rem 2rem rgba(0, 0, 0, 0.6);
  margin: 1rem 1rem;
  padding: 2rem;
  transition: all 0.6s;
  // &:hover {
  //   transform: scale(1.2);
  //   box-shadow: 0.2rem 1rem 3rem rgba(0, 0, 0, 0.6);
  // }
  // &:hover &__img {
  //   transform: scale(2);
  //   filter:  drop-shadow(5px 5px 5px #000);
  // }
  &__img--show & {
    transform: scale(2);
  }
  &__img {
    margin-top: 2rem;
    margin-bottom: 1rem;
    width: 100px;
    padding: 1rem;
    transform: scale(2);
    filter: brightness(0%) drop-shadow(5px 5px 5px #000);
    &--show {
      width: 100px;
      margin-top: 3rem;
      margin-bottom: 1rem;
      padding: 1rem;
      transform: scale(2);
      filter: brightness(100%) drop-shadow(5px 5px 5px #000);
    }
  }
}
.adivinando {
  display: inline-block;
  &__input {
    float: left;
    width: 70%;
    margin-bottom: 2rem;
  }
  button {
    float: right;
    width: 30%;
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
.footer {
  padding: 2rem;
  background-color: #4b50a944;
  color: #f5d536;
}
.go-up {
  position: fixed;
  right: 4rem;
  bottom: 4rem;
  z-index: 100;
  @media (max-width: 600px) {
    width: 3rem;
    right: 1rem;
    bottom: 1rem;
  }
}
@import "../node_modules/nes.css/css/nes.css";
@import url("https://fonts.googleapis.com/css2?family=Press+Start+2P&display=swap");
</style>
