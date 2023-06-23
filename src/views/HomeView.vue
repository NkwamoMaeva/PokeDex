<template>
  <div class="bg-red-lighten-5 rounded-lg search-box">
    <v-row>
      <v-col cols="8">
        <div class="d-flex flex-column">
          <span class="text-h5 font-weight-bold text-red-darken-1">Find a Pokemon</span>
          <span class="text-body-2 text-medium-emphasis">
            Bienvenue sur la page du Pokédex entièrement dédié à Pokémon GO. Vous pouvez retrouver
            l'ensemble des informations des Pokémon présents dans Pokémon
          </span>
          <v-text-field
            class="mt-6 bg-grey-lighten-5"
            label="Search"
            prepend-inner-icon="mdi-magnify"
            variant="outlined"
            hide-details
          ></v-text-field>
        </div>
      </v-col>
      <v-spacer></v-spacer>
      <v-col cols="3" class="search-box-img-box">
        <img src="@/assets/img/pokedex.png" width="300" class="search-box-img" />
      </v-col>
    </v-row>
  </div>

  <v-container> </v-container>
</template>

<script>
import { defineComponent } from 'vue'

export default defineComponent({
  name: 'HomeView',

  components: {}
})
</script>

<style lang="scss" scoped>
.search-box {
  margin: 24px 64px;
  padding: 24px;
  .search-box-img-box {
    position: relative;
    .search-box-img {
      position: absolute;
      top: -50px;
    }
  }
}
</style>
