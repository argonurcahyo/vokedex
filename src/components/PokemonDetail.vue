<template>
  <div class="detail">
    <div class="detail-view" v-if="show">
      <div class="image" v-if="pokemon">
        <img :src="imageUrl + pokemon.id + '.png'" alt="">
      </div>
      <div class="data" v-if="pokemon">
        <h2>{{ pokemon.name }}</h2>
        <p>{{ species?.genera?.find(g => g.language.name === "en")?.genus }}</p>
        <div class="property">
          <div class="left">Base Experience</div>
          <div class="right">{{ pokemon.base_experience }} XP</div>
        </div>
        <div class="property">
          <div class="left">Height</div>
          <div class="right">{{ pokemon.height / 10 }} m</div>
        </div>
        <div class="property">
          <div class="left">Weight</div>
          <div class="right">{{ pokemon.weight / 10 }} kg</div>
        </div>
        <div class="property">
          <div class="left">Generation</div>
          <div class="right">{{ species?.generation?.name?.replace("generation-", "")?.toUpperCase() }}</div>
        </div>
        <div class="property">
          <div>{{ species?.flavor_text_entries?.find(t => t.language.name === "en")?.flavor_text?.replace("", " ") }}
          </div>
        </div>
        <h3>Pokemon Types</h3>
        <div class="types">
          <div class="type" v-for="(value, index) in pokemon.types" :key="'value' + index">
            {{ value.type.name }}
          </div>
        </div>
        <h3>Abilities</h3>
        <div class="abilities">
          <div class="ability" v-for="(value, index) in pokemon.abilities" :key="'value' + index">
            {{ value.ability.name }}
          </div>
        </div>
      </div>
      <h2 v-else>The Pokemon was not found</h2>
      <button class="close" @click="closeDetail">close</button>
    </div>
    <i v-else class="fas fa-spinner fa-spin"></i>
  </div>
</template>

<script>
export default {
  props: [
    'pokemonUrl',
    'imageUrl'
  ],
  data: () => {
    return {
      show: false,
      pokemon: {},
      species: {}
    }
  },
  methods: {
    fetchData() {
      let req = new Request(this.pokemonUrl)
      fetch(req)
        .then(res => {
          if (res.status === 200)
            return res.json()
        })
        .then(data => {
          console.log(data)
          this.pokemon = data
          this.show = true
          return this.pokemon
        })
        .then(d => {
          let req2 = new Request(d.species.url)
          return fetch(req2)
        })
        .then(res => {
          if (res.status === 200)
            return res.json()
        })
        .then(data => {
          console.log(data)
          this.species = data
        })
        .catch(error => {
          console.log(error)
        })
    },
    closeDetail() {
      this.$emit('closeDetail')
    }
  },
  created() {
    this.fetchData()
  }
}
</script>

<style lang="scss" scoped>
.detail {
  display: flex;
  justify-content: center;
  align-items: flex-start;
  position: fixed;
  top: 0;
  left: 0;
  padding: 90px 10px 10px;
  width: calc(100% - 20px);
  height: calc(100vh - 20px);
  background: rgba($color: #000000, $alpha: .7);

  .detail-view {
    display: flex;
    justify-content: center;
    align-items: center;
    flex-direction: column;
    position: relative;
    width: 100%;
    max-width: 510px;
    padding: 50px 0 0;
    background-color: #fff;
    border-radius: 5px;
    box-shadow: 0 15px 30px rgba(0, 0, 0, .2),
      0 10px 10px rgba(0, 0, 0, .2);

    .image {
      display: flex;
      justify-content: center;
      align-items: center;
      position: absolute;
      top: -60px;
      width: 120px;
      height: 120px;
      background-color: #333;
      border-radius: 50%;
      overflow: hidden;
      box-shadow: 0 15px 30px rgba(0, 0, 0, .2),
        0 10px 10px rgba(0, 0, 0, .2);
    }

    h2 {
      text-transform: capitalize;
    }

    .data {
      display: flex;
      justify-content: flex-start;
      align-items: center;
      flex-direction: column;
      width: 100%;
      margin-bottom: 40px;

      .property {
        width: 90%;
        max-width: 400px;
        border-bottom: 1px solid #ccc;
        margin-bottom: 10px;

        .left {
          float: left;
        }

        .right {
          float: right;
        }
      }

      h3 {
        width: 90%;
        max-width: 400px;
        border-bottom: 1px solid #ccc;
      }

      .types,
      .abilities {
        display: flex;
        justify-content: flex-start;
        flex-wrap: wrap;
        width: 90%;
        max-width: 400px;

        .type,
        .ability {
          margin: 0 10px 10px 0;
          padding: 5px 10px;
          border-radius: 20px;
          color: #fff;
          font-size: 1rem;
          letter-spacing: 2px;
          text-transform: capitalize;
          word-wrap: none;
          word-break: keep-all;
        }

        .type {
          background-color: #0A2E50;
        }

        .ability {
          background-color: #C73015;
        }
      }
    }

    .close {
      outline: none;
      border: none;
      border-radius: 5px;
      background-color: #333;
      color: #efefef;
      padding: 10px 20px;
      margin-bottom: 20px;
      font-size: 1.2rem;
      cursor: pointer;
    }
  }

  i {
    font-size: 2rem;
    color: #efefef;
  }
}
</style>