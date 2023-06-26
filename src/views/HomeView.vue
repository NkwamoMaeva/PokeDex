<template>
  <div class="bg-red-lighten-5 rounded-lg search-container my-6 mx-md-16 mx-sm-4">
    <div class="d-flex flex-row justify-space-between">
      <div class="search-box">
        <div class="d-flex flex-column">
          <h5 class="font-weight-bold text-red-darken-1">Find a Pokemon</h5>
          <h6 class="text-medium-emphasis">
            Bienvenue sur la page du Pokédex entièrement dédié à Pokémon GO. Vous pouvez retrouver
            l'ensemble des informations des Pokémon présents dans Pokémon
          </h6>
          <div class="search-input">
            <v-text-field
              ref="searchInput"
              v-model="search"
              class="mt-6 bg-grey-lighten-5"
              label="search"
              prepend-inner-icon="mdi-magnify"
              append-inner-icon="mdi-filter-variant"
              variant="outlined"
              hide-details
              @click:append-inner="toogleFilter"
            >
            </v-text-field>
            <v-expand-transition>
              <div v-show="openFilter" id="filter-box" class="rounded-b-lg elevation-2">
                <v-card-text>
                  <h5 class="mb-2">Choose Types</h5>

                  <v-chip-group v-model="typeFilter" color="error" column>
                    <v-chip
                      filter
                      :value="type.name"
                      size="x-small"
                      variant="outlined"
                      v-for="(type, index) in types"
                      :key="index"
                    >
                      {{ type.name }}
                    </v-chip>
                  </v-chip-group>

                  <h5 class="mb-2 mt-6">Choose base experience</h5>
                  <v-range-slider
                    step="10"
                    min="20"
                    max="390"
                    v-model="expFilter"
                    thumb-label="always"
                    color="error"
                  ></v-range-slider>
                </v-card-text>
              </div>
            </v-expand-transition>
          </div>
        </div>
      </div>
      <div class="search-img-box d-none d-lg-flex d-xl-none">
        <img src="@/assets/img/pokedex.png" width="300" class="search-img" />
      </div>
    </div>
  </div>

  <v-container class="d-flex flex-column" v-if="filteredData.length > 0">
    <div class="pokemon-list">
      <div v-for="(item, index) in pokemonList" :key="index">
        <item-list v-if="item.details" :pokemon="item.details"></item-list>
      </div>
    </div>
    <div v-if="filteredData.length > 25">
      <v-pagination
        v-model="page"
        class="my-4 mx-auto pagination"
        :length="filteredData.length / 25"
        @update:modelValue="onChangePage"
        :total-visible="5"
      ></v-pagination>
    </div>
  </v-container>
</template>

<script>
// Components
import ItemList from '@/components/pokemon_list/ItemList.vue'
import axios from 'axios'
import { ref, watch, onMounted } from 'vue'
import { useRoute, useRouter } from 'vue-router'

export default {
  name: 'HomeView',

  components: {
    ItemList
  },
  setup() {
    const pokemonList = ref([])
    const filteredData = ref([])
    const totalData = ref([])
    const route = useRoute()
    const router = useRouter()
    const search = ref('')
    const openFilter = ref(false)
    const types = ref([])
    const typeFilter = ref()
    const expFilter = ref([20, 390])
    const searchInput = ref(null)

    const page = ref(route.query.page ? Number(route.query.page) : 1)

    onMounted(() => {
      window.addEventListener('click', function (e) {
        if (document.getElementById('filter-box').contains(e.target)) {
          // Clicked in box
          searchInput.value.focus()
        } else {
          // Clicked outside the box
          if (!searchInput.value.active) {
            openFilter.value = false
          }
        }
      })
    })

    axios.get('https://pokeapi.co/api/v2/type').then((response) => {
      types.value = response.data.results
    })

    async function fetchData() {
      let result = []
      const res = await fetch('https://pokeapi.co/api/v2/pokemon?offset=0&limit=200')
      result = await res.json()

      return await Promise.all(
        result.results.map(async (element) => {
          const response = await axios.get(element.url)
          element.details = response.data
          return element
        })
      ).then((value) => {
        totalData.value = value
        filteredData.value = value
        onChangePage()
      })
    }
    fetchData()

    function fetchDataPerPage(page, perPage, data) {
      pokemonList.value = data.slice((page - 1) * perPage, page * perPage)
    }
    watch(
      () => [search.value, typeFilter.value, expFilter.value],
      async (newFilter) => {
        let result = []
        console.log(newFilter)
        if (newFilter[1]) {
          try {
            const res = await fetch(`https://pokeapi.co/api/v2/type/${newFilter[1]}`)
            result = await res.json()
            result = result.pokemon
            return await Promise.all(
              result.map(async (element) => {
                const response = await axios.get(element.pokemon.url)
                element.pokemon.details = response.data
                return element.pokemon
              })
            ).then((value) => {
              filteredData.value = value.filter(
                (item) =>
                  item.name.toLowerCase().startsWith(newFilter[0].toLowerCase()) &&
                  item.details.base_experience >= newFilter[2][0] &&
                  item.details.base_experience <= newFilter[2][1]
              )
              onChangePage()
            })
          } catch (error) {
            console.log(error)
          }
        } else {
          filteredData.value = totalData.value.filter(
            (item) =>
              item.name.toLowerCase().startsWith(newFilter[0].toLowerCase()) &&
              item.details.base_experience >= newFilter[2][0] &&
              item.details.base_experience <= newFilter[2][1]
          )
        }

        page.value = 1
        onChangePage()
      }
    )

    function onChangePage() {
      router.push({ path: '', query: { page: page.value } })
      fetchDataPerPage(page.value, 25, filteredData.value)
    }

    function toogleFilter() {
      openFilter.value = !openFilter.value
      if (!openFilter.value) {
        searchInput.value.blur()
      }
    }

    return {
      page,
      types,
      search,
      typeFilter,
      expFilter,
      openFilter,
      pokemonList,
      totalData,
      filteredData,
      searchInput,
      onChangePage,
      fetchData,
      fetchDataPerPage,
      toogleFilter
    }
  }
}
</script>

<style lang="scss" scoped>
.search-container {
  padding: 24px;

  .search-box {
    width: 100%;
    .search-input {
      position: relative;
      #filter-box {
        background-color: white;
        position: absolute;
        z-index: 10;
        width: 100%;
        height: 250px;
      }
    }
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
