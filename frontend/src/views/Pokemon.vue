<template>
  <div>
    <PokemonCreature :msg="this.$route.params.pokemon" />
  </div>
</template>

<script>
// @ is an alias to /src
import PokemonCreature from "@/components/PokemonCreature.vue";

export default {
  name: "Pokemon",
  components: {
    PokemonCreature
  }
};
</script>
