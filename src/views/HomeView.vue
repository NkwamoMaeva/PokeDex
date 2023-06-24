<template>
  <div class="bg-red-lighten-5 rounded-lg search-container my-6 mx-md-16 mx-sm-4">
    <div class="d-flex flex-row justify-space-between">
      <div class="search-box">
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
      </div>
      <div class="search-img-box d-none d-lg-flex d-xl-none">
        <img src="@/assets/img/pokedex.png" width="300" class="search-img" />
      </div>
    </div>
  </div>

  <v-container class="d-flex flex-column" v-if="pokemonList.results">
    <div class="pokemon-list">
      <item-list
        v-for="(item, index) in pokemonList.results"
        :pokemonUrl="item.url"
        :key="index"
      ></item-list>
    </div>
    <div>
      <v-pagination
        v-model="page"
        class="my-4 mx-auto pagination"
        :length="Math.round(pokemonList.count / 25)"
        @update:modelValue="onChange"
      ></v-pagination>
    </div>
  </v-container>
</template>

<script>
// Components
import ItemList from '@/components/pokemon_list/ItemList.vue'
import { ref, watch } from 'vue'
import { useRouter } from 'vue-router'

export default {
  name: 'HomeView',

  components: {
    ItemList
  },
  setup() {
    const pokemonList = ref({})
    const router = useRouter()
    const page = ref(
      router.currentRoute.value.query.page ? Number(router.currentRoute.value.query.page) : 1
    )

    fetchData(page.value)

    // fetch the user information when params change
    watch(
      () => router.currentRoute.value.query.page,
      (newPage) => {
        fetchData(newPage)
      }
    )

    async function fetchData(page) {
      try {
        const res = await fetch(
          `https://pokeapi.co/api/v2/pokemon?offset=${(page - 1) * 25}&limit=25`
        )
        pokemonList.value = await res.json()
      } catch (error) {
        console.log(error)
      }
    }

    function onChange(currentPage) {
      router.push({ path: '', query: { page: currentPage } })
    }

    return {
      page,
      pokemonList,
      onChange,
      fetchData
    }
  }
}
</script>

<style lang="scss" scoped>
.search-container {
  padding: 24px;

  .search-box {
    width: 100%;
  }
  .search-img-box {
    position: relative;
    margin-left: 100px;
    width: 300px;

    .search-img {
      position: absolute;
      left: -20px;
      top: -50px;
    }
  }
}
.pokemon-list {
  display: grid;
  grid-gap: 15px;
  grid-template-columns: repeat(auto-fit, minmax(230px, 1fr));
}
@media screen and (min-width: 960px) {
  .pagination {
    width: 50%;
  }
}
</style>
