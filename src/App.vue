<template>
  <v-app>
    <v-app-bar
      app
      color="primary"
      dark
    >
      <div class="d-flex align-center">
        <v-btn to="/" text>
        <v-img
          alt="Vuetify Logo"
          class="shrink mr-2"
          contain
          src="./assets/SquirrellogicEve.png"
          transition="scale-transition"
          width="40"
        />

        <span class="mr-2"> API Testing Corp | Buyback Service</span>
        </v-btn>
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
