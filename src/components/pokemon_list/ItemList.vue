<template>
  <v-skeleton-loader
    :loading="pokemon.sprites === undefined"
    class="border"
    type="image,list-item-two-line,chip"
  >
    <v-card class="w-100 item-list" @click="goToDetails(pokemon.id)">
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
        <div class="d-flex flex-row justify-space-between">
          <h6 class="me-1">#{{ pokemon.id }}</h6>
          <h6 class="me-1">Exp: {{ pokemon.base_experience }}</h6>
        </div>

        <v-card-title>{{ pokemon.name }}</v-card-title>
        <div>
          <v-chip-group>
            <v-chip size="x-small" v-for="(type, index) in pokemon.types" :key="index">{{
              type.type.name
            }}</v-chip>
          </v-chip-group>
        </div>
      </v-card-item>
    </v-card>
  </v-skeleton-loader>
</template>

<script>
import { useRouter } from 'vue-router'

export default {
  props: ['pokemon'],
  setup() {
    const router = useRouter()

    function goToDetails(id) {
      router.push({ name: 'details', params: { id: id } })
    }

    return {
      goToDetails
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
