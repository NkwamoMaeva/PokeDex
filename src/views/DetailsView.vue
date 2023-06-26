<template>
  <v-container v-if="pokemon.sprites" class="d-flex container justify-space-between">
    <div class="w-50 d-flex flex-column">
      <div class="d-flex flex-row align-center">
        <h1>{{ pokemon.name }}</h1>
        <span class="text-grey-lighten-1 ml-3">#{{ pokemon.id }}</span>
      </div>

      <v-chip-group>
        <v-chip size="x-small" v-for="(type, index) in pokemon.types" :key="index">{{
          type.type.name
        }}</v-chip>
      </v-chip-group>
      <v-img max-width="450" max-height="400" :src="version"></v-img>
    </div>
    <div class="d-flex flex-column">
      <div class="mb-6">
        <h6 class="">Abilities</h6>
        <v-chip-group>
          <v-chip
            size="small"
            :disabled="item.is_hidden"
            v-for="(item, index) in pokemon.abilities"
            :key="index"
            >{{ item.ability.name }}</v-chip
          >
        </v-chip-group>
      </div>
      <div class="mb-6">
        <h6 class="">Versions</h6>
        <v-chip-group v-model="version" selected-class="text-red-darken-1" mandatory>
          <v-chip :value="pokemon.sprites.other['home']['front_default']">Normal</v-chip>
          <v-chip :value="pokemon.sprites.other['home']['front_shiny']">Shiny</v-chip>
        </v-chip-group>
      </div>
      <v-row class="my-3 mb-6">
        <v-sheet
          elevation="2"
          class="d-flex flex-column pa-2 mr-2 justify-center align-center"
          height="100"
          width="100"
          rounded
        >
          <span class="mb-2 desc-title">Weight</span>
          <span class="desc-value">{{ pokemon.weight }}kg</span>
        </v-sheet>
        <v-sheet
          elevation="2"
          class="d-flex flex-column pa-2 mr-2 justify-center align-center"
          height="100"
          width="100"
          rounded
        >
          <span class="mb-2 desc-title">Height</span>
          <span class="desc-value">{{ pokemon.height / 10 }}m</span>
        </v-sheet>
      </v-row>
      <div>
        <Radar class="stat-chart" :data="stats" :option="option" />
      </div>
    </div>
  </v-container>
</template>

<script>
import { useRoute } from 'vue-router'
import { Radar } from 'vue-chartjs'
import { ref } from 'vue'
import {
  Chart as ChartJS,
  RadialLinearScale,
  PointElement,
  LineElement,
  Filler,
  Tooltip,
  Legend
} from 'chart.js'
ChartJS.register(RadialLinearScale, PointElement, LineElement, Filler, Tooltip, Legend)
export default {
  components: {
    Radar
  },
  setup() {
    const route = useRoute()
    const pokemon = ref({})
    const version = ref()
    const options = {
      responsive: true,
      maintainAspectRatio: false
    }
    const stats = ref({
      labels: [],
      datasets: [
        {
          label: 'Base statistics',
          backgroundColor: 'rgba(255,99,132,0.2)',
          borderColor: 'rgba(255,99,132,1)',
          pointBackgroundColor: 'rgba(255,99,132,1)',
          pointBorderColor: '#fff',
          pointHoverBackgroundColor: '#fff',
          pointHoverBorderColor: 'rgba(255,99,132,1)',
          data: []
        }
      ]
    })

    async function fetchItem() {
      const res = await fetch(`https://pokeapi.co/api/v2/pokemon/${route.params.id}`)
      pokemon.value = await res.json()
      version.value = pokemon.value.sprites.other['home']['front_default']
      pokemon.value.stats.forEach((el) => {
        console.log(el)
        stats.value.labels.push(el.stat.name)
        stats.value.datasets[0].data.push(el.base_stat)
      })
    }

    fetchItem()
    console.log(pokemon.value)

    return {
      pokemon,
      version,
      stats,
      options,
      fetchItem
    }
  }
}
</script>
<style lang="scss">
.container {
  flex-direction: row;
  .desc-title {
    font-size: 14px;
  }
  .desc-value {
    font-size: 10px;
  }
  .stat-chart {
    max-width: 400px;
    max-height: 400px;
  }
}

@media screen and (max-width: 960px) {
  .container {
    flex-direction: column !important;
    align-items: center;
  }
}
</style>
;
