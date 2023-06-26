<template>
  <v-app>
    <v-toolbar class="white" color="white" density="comfortable">
      <a href="/" class="text-decoration-none">
        <v-toolbar-title>
          <div class="d-flex flex-row align-center">
            <v-icon size="large" class="mx-2" color="red-darken-1">mdi-pokeball</v-icon>
            <strong> <strong class="text-red-darken-1">Poke</strong>Dex </strong>
          </div>
        </v-toolbar-title>
      </a>

      <v-spacer></v-spacer>
      <v-btn icon>
        <v-icon color="red-darken-1">mdi-dots-vertical</v-icon>
      </v-btn>
    </v-toolbar>
    <v-main class="bg-grey-lighten-5 pt-8">
      <router-view />
    </v-main>
  </v-app>
</template>

<script>
export default {
  name: 'App',

  data: () => ({
    //
  })
}
</script>
<style>
header {
  position: sticky;
  z-index: 1;
  top: 0;
}
* {
  font-family: 'Press Start 2P' !important;
}
</style>
