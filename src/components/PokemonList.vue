<template>
  <div class="list">
    <article v-for="(pokemon, index) in pokemons" :key="'poke' + index" @click="setPokemonUrl(pokemon.url)">
      <!-- <div class="poke-id">#{{ pokemon.id }}</div>
      <img :src="imageUrl + pokemon.id + '.png'" width="96" height="96" alt="">
      <h3>{{ pokemon.name }}</h3> -->
      <PokemonCard :imageUrl="imageUrl" :pokemon="pokemon" />
    </article>
    <div id="scroll-trigger" ref="infinitescrolltrigger">
      <i class="fa fa-spinner fa-spin"></i>
    </div>
  </div>
</template>

<script>
import PokemonCard from './PokemonCard.vue'
export default {
  props: [
    "imageUrl",
    "apiUrl"
  ],
  data: () => {
    return {
      pokemons: [],
      nextUrl: "",
      currentUrl: ""
    };
  },
  components: {
    PokemonCard,
  },
  methods: {
    fetchData() {
      let req = new Request(this.currentUrl);
      fetch(req)
        .then(res => {
          if (res.status === 200)
            return res.json();
        })
        .then(data => {
          this.nextUrl = data.next;
          data.results.forEach(pokemon => {
            pokemon.id = pokemon.url.split("/").filter(part => !!part).pop();
            this.pokemons.push(pokemon);
          });
        })
        .catch(error => {
          console.log(error);
        });
    },
    scrollTrigger() {
      const observer = new IntersectionObserver(entries => {
        entries.forEach(entry => {
          if (entry.intersectionRatio > 0 && this.nextUrl) {
            this.next();
          }
        });
      });
      observer.observe(this.$refs.infinitescrolltrigger);
    },
    next() {
      this.currentUrl = this.nextUrl;
      this.fetchData();
    },
    setPokemonUrl(url) {
      this.$emit("setPokemonUrl", url);
    }
  },
  created() {
    this.currentUrl = this.apiUrl;
    this.fetchData();
  },
  mounted() {
    this.scrollTrigger();
  },
  components: { PokemonCard }
}
</script>

<style lang="scss" scoped>
.list {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(150px, 1fr));
  gap: 10px;
  width: 100%;
  max-width: 510px;

  article {
    position: relative;
    height: 150px;
    background-color: #efefef;
    text-align: center;
    text-transform: capitalize;
    border-radius: 5px;
    cursor: pointer;
    box-shadow: 0 15px 30px rgba(0, 0, 0, .2),
      0 10px 10px rgba(0, 0, 0, .2);

    h3 {
      margin: 0;
    }
  }
}

#scroll-trigger {
  display: flex;
  justify-content: center;
  align-items: center;
  width: 100%;
  height: 150px;
  font-size: 2rem;
  color: #efefef;
}

.poke-id {
  position: absolute;
  top: 0;
  left: 0;
  font-size: .7rem;
  padding: 3px 5px;
  width: auto;
  height: auto;
  border-radius: 5px 0 5px 0;
  background: #1f9625;
  color: #efefef
}
</style>