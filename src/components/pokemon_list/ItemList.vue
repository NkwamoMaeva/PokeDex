<template>
  <v-skeleton-loader
    :loading="pokemon.sprites === undefined"
    class="border"
    type="image,list-item-two-line,chip"
  >
    <v-card class="w-100 item-list">
      <v-img
        height="200"
        class="bg-blue-grey-lighten-5"
        :src="pokemon.sprites.other['official-artwork']['front_default']"
      >
        <template v-slot:placeholder>
          <div class="d-flex align-center justify-center fill-height">
            <v-progress-circular color="grey-lighten-4" indeterminate></v-progress-circular>
          </div>
        </template>
      </v-img>

      <v-card-item>
        <span class="me-1 text-caption">#{{ pokemon.id }}</span>

        <v-card-title>{{ pokemon.name }}</v-card-title>
        <div>
          <v-chip-group>
            <v-chip size="small" v-for="(type, index) in pokemon.types" :key="index">{{
              type.type.name
            }}</v-chip>
          </v-chip-group>
        </div>
      </v-card-item>
    </v-card>
  </v-skeleton-loader>
</template>

<script>
import { ref, watch } from 'vue'

export default {
  props: ['pokemonUrl'],
  setup(props) {
    const pokemon = ref({})

    fetchData(props.pokemonUrl)
    watch(
      () => props.pokemonUrl,
      (url) => {
        fetchData(url)
      }
    )

    async function fetchData(url) {
      try {
        const res = await fetch(url)
        pokemon.value = await res.json()
      } catch (error) {
        console.log(error)
      }
    }
    return {
      pokemon,
      fetchData
    }
  }
}
</script>

<style scoped>
.item-list {
  cursor: pointer;
  transition: all 0.5s;
  &:hover {
    transform: translateY(-6px);
  }
}
</style>
