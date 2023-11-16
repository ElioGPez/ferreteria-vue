<template>
  <div id="app">
    <v-app id="inspire">
      <v-overlay :value="drawer" z-index="4"> </v-overlay>
      <v-navigation-drawer
        v-model="drawer"
        app
        clipped
        hide-overlay
        :style="{ top: $vuetify.application.top + 'px', zIndex: 6 }"
      >
        <!--v-list nav dense>
          <v-list-item-group
            v-model="group"
            active-class="deep-purple--text text--accent-4"
          >
            <router-link to="/">
              <v-list-item>
                <v-list-item-icon>
                  <v-icon>mdi-home</v-icon>
                </v-list-item-icon>
                <v-list-item-title>Home</v-list-item-title>
              </v-list-item>
            </router-link>
            <router-link to="/about">
              <v-list-item>
                <v-list-item-icon>
                  <v-icon>mdi-account</v-icon>
                </v-list-item-icon>
                <v-list-item-title>About</v-list-item-title>
              </v-list-item>
            </router-link>
          </v-list-item-group>
        </v-list-->

        <v-list nav dense>
          <div v-for="(link, i) in links" :key="i">
            <v-list-item
              v-if="!link.subLinks"
              :to="link.to"
              :active-class="'v-window-item--active'"
              class="v-list-item"
              @click="drawer = false"
            >
              <v-list-item-icon>
                <v-icon>{{ link.icon }}</v-icon>
              </v-list-item-icon>

              <v-list-item-title v-text="link.text" />
            </v-list-item>

            <v-list-group
              v-else
              :key="link.text"
              :prepend-icon="link.icon"
              :value="false"
            >
              <template v-slot:activator>
                <v-list-item-title>{{ link.text }}</v-list-item-title>
              </template>

              <v-list-item
                v-for="sublink in link.subLinks"
                :to="sublink.to"
                :key="sublink.text"
                @click="drawer = false"
              >
                <v-list-item-icon>
                  <v-icon>{{ sublink.icon }}</v-icon>
                </v-list-item-icon>
                <v-list-item-title>{{ sublink.text }}</v-list-item-title>
              </v-list-item>
            </v-list-group>
          </div>
        </v-list>
      </v-navigation-drawer>

      <v-app-bar app clipped-left dark color="#447fa6">
        <v-app-bar-nav-icon @click.stop="drawer = !drawer"></v-app-bar-nav-icon>
        <div class="d-flex align-center">
          <v-img
            alt="Vuetify Logo"
            class="shrink mr-2"
            contain
            src="img/logo.png"
            transition="scale-transition"
            width="50"
          />

        </div>
        <h2>Sistema Ferreteríe</h2>
        <!--v-toolbar-title>Sistema Ferretería</v-toolbar-title-->
      </v-app-bar>

      <v-main class="main">
        <router-view />
      </v-main>
    </v-app>
  </div>

  <!--v-app>
        <v-toolbar
      dark
      prominent
      src="https://cdn.vuetifyjs.com/images/backgrounds/vbanner.jpg"
    >
      <v-app-bar-nav-icon></v-app-bar-nav-icon>

      <v-toolbar-title>Vuetify</v-toolbar-title>

      <v-spacer></v-spacer>

      <v-btn icon>
        <v-icon>mdi-export</v-icon>
      </v-btn>
    </v-toolbar>
      <v-card
    class="mx-auto overflow-hidden"
    height="400"
  >
    <v-app-bar
      color="deep-purple"
      dark
    >
      <v-app-bar-nav-icon @click="drawer = true"></v-app-bar-nav-icon>

      <v-toolbar-title>Title</v-toolbar-title>
    </v-app-bar>

    <v-navigation-drawer
      v-model="drawer"
      absolute
      temporary
    >
      <v-list
        nav
        dense
      >
        <v-list-item-group
          v-model="group"
          active-class="deep-purple--text text--accent-4"
        >
          <v-list-item>
            <v-list-item-icon>
              <v-icon>mdi-home</v-icon>
            </v-list-item-icon>
            <v-list-item-title>Home</v-list-item-title>
          </v-list-item>

          <v-list-item>
            <v-list-item-icon>
              <v-icon>mdi-account</v-icon>
            </v-list-item-icon>
            <v-list-item-title>Account</v-list-item-title>
          </v-list-item>
        </v-list-item-group>
      </v-list>
    </v-navigation-drawer>
  </v-card>
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

        <v-img
          alt="Vuetify Name"
          class="shrink mt-1 hidden-sm-and-down"
          contain
          min-width="100"
          src="https://cdn.vuetifyjs.com/images/logos/vuetify-name-dark.png"
          width="100"
        />
      </div>

      <v-spacer></v-spacer>

      <v-btn
        href="https://github.com/vuetifyjs/vuetify/releases/latest"
        target="_blank"
        text
      >
        <span class="mr-2">Latest Release</span>
        <v-icon>mdi-open-in-new</v-icon>
      </v-btn>
    </v-app-bar>

    <v-main>
      <router-view/>
    </v-main>
  </v-app-->
</template>

<script>
import Home from "@/views/HomeView.vue";
export default {
  name: "App",

  data: () => ({
    drawer: false,
    group: null,

    links: [
      {
        to: "/",
        icon: "mdi-view-dashboard",
        text: "Dashboard",
      },
      {
        to: "/articles",
        icon: "mdi-screwdriver",
        text: "Artículos",
      },
      {
        to: "/about",
        icon: "mdi-account",
        text: "Acerca de",
      },
      {
        icon: "mdi-folder",
        text: "Templates",
        subLinks: [
          {
            text: "View Templates",
            to: "/templates",
            icon: "mdi-view-list",
          },
          {
            text: "New Template",
            to: "/templates/new",
            icon: "mdi-plus",
          },
        ],
      },
      {
        icon: "mdi-application",
        text: "Applications",
        subLinks: [
          {
            text: "View Applications",
            to: "/apps",
            icon: "mdi-view-list",
          },
          {
            text: "New Application",
            to: "/apps-2",
            icon: "mdi-plus",
          },
        ],
      },
    ],
  }),
};
</script>

<style scoped>
.v-application--is-ltr
  .v-list--dense.v-list--nav
  .v-list-group--no-action
  > .v-list-group__items
  > .v-list-item {
  padding: 0 8px;
}
.main {
  background-color: aliceblue;
  padding: 70pt 20pt 0 20pt !important;
}

</style>