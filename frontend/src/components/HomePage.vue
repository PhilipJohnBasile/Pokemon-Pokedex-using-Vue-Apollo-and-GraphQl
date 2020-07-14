<template>
  <div class="hello">
    <div class="tabs">
      <input
        class="input"
        name="tabs"
        type="radio"
        id="tab-1"
        checked="checked"
        v-on:change="showAll()"
      />
      <label class="label" for="tab-1">All</label>
      <input
        class="input"
        name="tabs"
        type="radio"
        id="tab-2"
        v-on:change="favsOnly()"
      />
      <label class="label" for="tab-2">Favorite</label>
    </div>

    <div class="inputBox">
      <input class="search" type="text" v-model="search" placeholder="Search" />
      <ApolloQuery
        class="dropdown"
        :query="require('../graphql/pokemonTypes.gql')"
      >
        <template v-slot="{ result: { loading, error, data } }">
          <div v-if="loading">loading</div>
          <div v-if="error">error</div>
          <div v-if="data">
            <select v-model="pokeType">
              <option></option>
              <option
                v-for="pokemon in data.pokemonTypes"
                v-bind:value="pokemon"
                :key="pokemon"
              >
                {{ pokemon }}
              </option>
            </select>
          </div>
        </template>
      </ApolloQuery>
      <div class="button__view" v-on:click="listGrid">View</div>
    </div>
    <ApolloQuery
      :query="require('../graphql/pokemons.gql')"
      :variables="{ search, pokeType, favs }"
      ref="queryComponentRef"
    >
      <template v-slot="{ result: { loading, error, data } }">
        <div v-if="loading">loading</div>
        <div v-if="error">error</div>
        <div v-if="data">
          <ul class="poke-box" v-bind:class="{ listGrid: isExpanded }">
            <li
              class="poke-box-item"
              v-for="pokemon in data.pokemons.edges"
              :key="pokemon.id"
            >
              <router-link
                :to="{ name: 'Pokemon', params: { pokemon: pokemon.name } }"
              >
                <img class="pokemon-image" v-bind:src="pokemon.image" />
              </router-link>
              <ul class="pokemon-titles">
                <li>{{ pokemon.name }}</li>
                <li>{{ pokemon.types.join(", ") }}</li>
                <li v-on:mouseover="hearts(pokemon.id)">
                  <ApolloMutation
                    v-if="pokemon.isFavorite"
                    :mutation="require('../graphql/unFavoritePokemon.gql')"
                    :variables="{ pokemonID }"
                    @done="onDoneUnFav"
                  >
                    <template v-slot="{ mutate }">
                      <form v-on:submit.prevent="mutate()">
                        <input
                          class="hidden"
                          v-model="pokemonID"
                          type="number"
                          name="unPokemonIDFav"
                          value="pokemonID"
                          id="unPokemonIDFav"
                        />
                        <button @click="mutate()" class="heart-full">❤️</button>
                      </form>
                    </template>
                  </ApolloMutation>
                  <ApolloMutation
                    v-else
                    :mutation="require('../graphql/favoritePokemon.gql')"
                    :variables="{ pokemonID }"
                    @done="onDoneFav"
                  >
                    <template v-slot="{ mutate }">
                      <form v-on:submit.prevent="mutate()">
                        <input
                          class="hidden"
                          v-model="pokemonID"
                          type="number"
                          name="pokemonIDFav"
                          value="pokemonID"
                          id="pokemonIDFav"
                        />
                        <button @click="mutate()" class="heart-empty">
                          ♡
                        </button>
                      </form>
                    </template>
                  </ApolloMutation>
                </li>
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
  name: "HomePage",
  data() {
    return {
      pokeType: "",
      search: "",
      pokemonID: "",
      isFavorite: Boolean,
      isExpanded: false,
      favs: false
    };
  },
  props: {
    msg: String
  },
  methods: {
    onDoneFav(val) {
      this.$refs.queryComponentRef.getApolloQuery().refetch();
      this.isFavorite = val;
    },
    onDoneUnFav(val) {
      this.isFavorite = val;
    },
    hearts: function(message) {
      this.$refs.queryComponentRef.getApolloQuery().refetch();
      this.pokemonID = message;
    },
    listGrid() {
      this.isExpanded = !this.isExpanded;
    },
    favsOnly() {
      this.$refs.queryComponentRef.getApolloQuery().refetch();
      this.favs = true;
      console.log(this.favs);
    },
    showAll() {
      this.favs = false;
      console.log(this.favs);
    }
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="scss">
.hidden {
  display: none;
}
h3 {
  margin: 40px 0 0;
}

li {
  display: inline-block;
  margin: 0 10px;
}

a {
  color: #42b983;
}

button {
  background: none;
  border: none;
  padding: 0;
  margin: 0;
  line-height: 20px;
  width: 20px;
  font-size: 20px;
  height: 0;
  outline: none;
  cursor: pointer;
}

ul {
  padding: 0;
  margin: 0;
  border: none;
  list-style-type: none;
}

.allFav {
  width: 100%;
}

.tabs {
  display: flex;
  flex-wrap: wrap;
  background: #e5e5e5;
  box-shadow: 0 48px 80px -32px rgba(0, 0, 0, 0.3);
}
.input {
  position: absolute;
  opacity: 0;
}

.label {
  width: auto;
  padding: 20px 30px;
  background: #e5e5e5;
  cursor: pointer;
  font-weight: bold;
  font-size: 18px;
  color: #7f7f7f;
  transition: background 0.1s, color 0.1s;
}
.label:hover {
  background: #71c1a1;
}
.label:active {
  background: #ccc;
}
.input:focus + .label {
  box-shadow: inset 0px 0px 0px 3px #2aa1c0;
  z-index: 1;
}
.input:checked + .label {
  background: #fff;
  color: #000;
}
.panel {
  display: none;
  padding: 20px 30px 30px;
  background: #fff;
}

.inputBox {
  display: flex;
  width: 100%;

  .search {
    display: flex;
    width: 65%;
    margin: 5px 5px 5px 0;
  }
  .dropdown {
    display: flex;
    width: 25%;
    margin: 5px 5px;

    div,
    select,
    option {
      display: flex;
      width: 100%;
    }
  }
  .button__view {
    display: inline-grid;
    width: 10%;
    background: #71c1a1;
    color: #ffffff;
    text-align: center;
    margin: 5px 0 5px 5px;
    cursor: pointer;
  }
}

.poke-box {
  display: flex;
  flex-wrap: wrap;
  padding-left: 0;
  justify-content: center;
}

.poke-box-item {
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

.poke-box-item a {
  display: flex;
  margin: 0 auto;
}

.pokemon-image {
  max-width: 90%;
  object-fit: scale-down;
  height: 15rem;
  padding: 1rem;
}

.pokemon-titles {
  background: #f3f3f3;
  padding: 1rem;
  vertical-align: bottom;
  width: 100%;
  box-sizing: border-box;
}

.pokemon-titles > li {
  font-size: 1.25rem;
  font-weight: bold;
}

.pokemon-titles > li + li {
  font-size: 1rem;
  font-weight: normal;
}

.listGrid {
  .poke-box-item {
    display: flex;
    flex: 0 0 100%;
    flex-flow: row;
  }
  a {
    width: 15%;
  }
  ul {
    width: 80%;
  }
  .pokemon-image {
    height: auto;
  }
}
</style>
