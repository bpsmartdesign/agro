<template>
  <v-app-bar app color="green darken-4" dark>
    <v-app-bar-nav-icon light @click.stop="setSidebar" />
    <v-toolbar-title style="width: 300px" class="ml-0 pl-4">
      <span class="hidden-sm-and-down" style="color: rgba(0, 0, 0, 0.54)">Projet Agro <span class="caption">(Dev-Wersion)</span></span>
    </v-toolbar-title>
    <v-text-field flat solo-inverted hide-details light prepend-inner-icon="mdi-magnify" label="Rechercher ..." class="hidden-sm-and-down elevation-3"/>
    <v-spacer />
    <!-- <v-btn icon><v-icon>mdi-apps</v-icon></v-btn> -->
    <v-badge color="warning" :content="forNotifications.length" :value="forNotifications.length" overlap>
      <v-menu transition="slide-y-transition" :offset-y="true" bottom>
        <template v-slot:activator="{ on }">
          <v-btn color="transparent" fab small light v-on="on">
            <v-icon small>mdi-bell</v-icon>
          </v-btn>
        </template>
        <v-list>
          <v-list-item v-for="(item, i) in forNotifications" :key="i">
            <v-list-item-title v-if="souscriptions">La souscription au service <strong class="green--text">{{ item.souscription }}</strong> se termine dans exactement <strong class="green--text">{{ item.nbrDaysRemaining }}</strong> Jours</v-list-item-title>
            <v-divider class="mx-4 my-0"></v-divider>
          </v-list-item>
        </v-list>
      </v-menu>
    </v-badge>
    <v-btn text icon light dense class="mr-2 ml-2"> <!-- Mode Plein écran -->
      <v-icon icon large>mdi-fullscreen</v-icon>
    </v-btn>
    <div class="text-center">
      <v-menu offset-y>
        <template v-slot:activator="{ on }">
          <v-chip v-if="currentUser" class="ma-2 elevation-3" light color="green darken-3" text-color="white" v-on="on">
            <v-avatar light left><v-icon light>mdi-account-circle</v-icon></v-avatar>
            {{ currentUser.username }}
          </v-chip>
        </template>
        <v-list class="mt-3">
          <v-list-item @click="logout">
            <v-list-item-icon><v-icon>mdi-logout</v-icon></v-list-item-icon>
            <v-list-item-content><v-list-item-title>Se déconnecter</v-list-item-title></v-list-item-content>
          </v-list-item>
        </v-list>
      </v-menu>
    </div>
  </v-app-bar>
</template>

<script>
export default {
  name: 'TopNav',
  data () {
    return {
      sidebarVisible: true,
      forNotifications: [],
      souscriptions: null,
      currentUser: null
    }
  },
  methods: {
    setSidebar () {
      let sidebar = document.querySelector('.sidebar')

      if (this.sidebarVisible) {
        sidebar.classList.add('hide-sidebar')
        setTimeout(() => {
          sidebar.classList.add('no-width')
        }, 100);
      } else {
        sidebar.classList.remove('hide-sidebar')
        sidebar.classList.remove('no-width')
      }

      this.sidebarVisible = !this.sidebarVisible
    },
    logout () {
      localStorage.removeItem('tmp-user-connected')
      window.location.reload()
    }
  },
  mounted () {
    if (window.localStorage.getItem('tmp-user-connected') !== null && window.localStorage.getItem('tmp-user-connected').length > 0) {
      this.currentUser = JSON.parse(window.localStorage.getItem('tmp-user-connected'))
    }
  }
}
</script>