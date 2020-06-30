<template>
  <v-app>
    <v-app-bar
      app
      color="primary"
      dark
    >
      <div class="d-flex align-center">
        <v-img
          alt="Vuetify Logo"
          class="shrink mr-2"
          contain
          src="https://cdn.vuetifyjs.com/images/logos/vuetify-logo-dark.png"
          transition="scale-transition"
          width="40"
        />

        <span class="mr-2">-HC- | Buyback Service</span>
      </div>

      <v-spacer></v-spacer>

      <v-btn to="/about" text>
        <span class="mr-2">About This Site</span>
      </v-btn>

      <v-btn to="/calculator" text>
        <span class="mr-2">Buyback Calculator</span>
      </v-btn>

      <v-btn to="/other" text>
      <span class="mr-2">Other Tools</span>
      </v-btn>
    </v-app-bar>

    <v-content>
      <router-view />
    </v-content>
  </v-app>
</template>

<script>
// import HelloWorld from './components/HelloWorld'

export default {
  name: 'App',

  components: {
    // HelloWorld
  },

  data: () => ({
    //
  })
}
</script>
