<template>
  <div>
    <ApolloQuery
      :query="require('../graphql/pokemon.gql')"
      :variables="{ name: msg }"
    >
      <template v-slot="{ result: { loading, error, data } }">
        <div v-if="loading">loading</div>
        <div v-if="error">error</div>
        <div v-if="data">
          <div class="poke-box-main">
            <img class="pokemon-image" v-bind:src="data.pokemonByName.image" />
            <div>
              <figure>
                <audio
                  class="audio_volume_only"
                  controls
                  :src="data.pokemonByName.sound"
                >
                  Your browser does not support the
                  <code>audio</code> element.
                </audio>
              </figure>
            </div>
            <div>
              <strong>{{ data.pokemonByName.name }}</strong>
            </div>
            <div>{{ data.pokemonByName.types.join(", ") }}</div>
            <div v-if="data.pokemonByName.isFavorite">❤️</div>
            <div v-else>♡</div>
            <div>CP: {{ data.pokemonByName.maxCP }}</div>
            <div>HP: {{ data.pokemonByName.maxHP }}</div>
            <div>
              <strong>Weight: </strong>
              <div>
                {{ data.pokemonByName.weight.minimum }} ~
                {{ data.pokemonByName.weight.maximum }}
              </div>
            </div>
            <div>
              <strong>Height: </strong>
              <div>
                {{ data.pokemonByName.height.minimum }} ~
                {{ data.pokemonByName.height.maximum }}
              </div>
            </div>
          </div>
          <h3 v-if="data.pokemonByName.evolutions.length > 0">Evolutions</h3>
          <ul class="poke-box">
            <li
              class="poke-box-bottom"
              v-for="evolution in data.pokemonByName.evolutions"
              :key="evolution.id"
            >
              <router-link
                :to="{ name: 'Pokemon', params: { pokemon: evolution.name } }"
              >
                <img class="pokemon-image" v-bind:src="evolution.image" />
              </router-link>
              <ul class="pokemon-titles">
                <li>{{ evolution.name }}</li>
                <li v-if="evolution.isFavorite">❤️</li>
                <li v-else>♡</li>
              </ul>
            </li>
          </ul>
        </div>
      </template>
    </ApolloQuery>
  </div>
</template>

<script>
export default {
  name: "PokemonCreature",
  data() {
    return {
      name: "Bulbasaur",
      pokemonID: "",
      isFavorite: Boolean
    };
  },
  props: {
    msg: String
  },
  methods: {
    onDoneFav(val) {
      this.isFavorite = val;
    },
    onDoneUnFav(val) {
      this.isFavorite = val;
    },
    hearts: function(message) {
      this.pokemonID = message;
      console.log(message);
    }
  }
};
</script>

<style scoped lang="scss">
.hidden {
  display: none;
}
.audio_volume_only {
  width: 50px;
}
h3 {
  margin: 40px 0 0;
  text-align: left;
}
ul {
  padding: 0;
  margin: 0;
  border: none;
  list-style-type: none;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
.poke-box-main,
.poke-box-bottom {
  border: 1px solid #cccccc;
}

.poke-box-main > div {
  background: #f3f3f3;
  display: flex;
  text-align: center;
  padding: 7px;
}

.poke-box-main > div strong {
  display: flex;
}

.poke-box-main > div > div {
  display: flex;
}

.pokemon-image {
  max-width: 100%;
  object-fit: scale-down;
  padding: 1rem;
}

.poke-box {
  display: flex;
  flex-wrap: wrap;
  padding-left: 0;
  justify-content: left;
}

.poke-box-bottom {
  list-style: none;
  flex: 0 0 31%;
  border: 1px solid #cccccc;
  padding: 0;
  margin: 0.5rem;
  display: flex;
  justify-content: space-between;
  flex-direction: column;
  box-sizing: border-box;
  background: #fff;
}

.poke-box-bottom a {
  display: flex;
  margin: 0 auto;
}

.poke-box-bottom .pokemon-image {
  max-width: 85%;
  object-fit: scale-down;
  height: 12rem;
  padding: 1rem;
}

.pokemon-titles {
  background: #f3f3f3;
  padding: 1rem;
  vertical-align: bottom;
}

.pokemon-titles > li {
  font-size: 1.25rem;
  font-weight: bold;
}

.pokemon-titles > li + li {
  font-size: 1rem;
  font-weight: normal;
}
</style>
