<template>
  <div class="sidebar elevation-20">
    <v-list class="v-list-sidebar" ref="sidebar">
      <v-list-item-group>
        <v-list-item v-for="(item, index) in items" :key="index" :to="item.uri">
          <v-list-item-icon>
            <v-icon light v-text="item.icon"></v-icon>
          </v-list-item-icon>
          <v-list-item-content>
              <v-list-item-title v-text="item.text"></v-list-item-title>
          </v-list-item-content>
        </v-list-item>
      </v-list-item-group>
    </v-list>
  </div>
</template>

<script>

export default {
  name: 'Sidebar',
  data () {
    return {
      items: [
        { icon: 'mdi-chart-bell-curve-cumulative', text: 'Gestion de la Paie', uri: '/app/reports' },
        { icon: 'mdi-account-cog-outline', text: 'Personnel', uri: '/app/dashboard' },
      ],
      model: 0,
    }
  },
  methods: {
  }
}
</script>

<style lang="scss" scoped>
.sub-menu {
  position: absolute;
  top: 80px;
  left: 0px;
  background: #eee;
  width: 200px;
  height: 100%;
  z-index: 9999;
  padding-top: 20px;

  .close-sub-menu {
    position: absolute;
    right: -15px;
    display: block;
    width: 40px;
    height: 40px;
    background: #eee;
    border-radius: 50%;
    text-align: center;
    line-height: 40px;
    cursor: pointer;

    &:hover {
      background: #ddd;
      transition-duration: .3s;
      transform: scale(.9) rotate(360deg);
    }
  }
}
.inherit {
  background: inherit !important;
  margin: 0 !important;
}
.fade-in-left {
  animation : fadeInLeft .3s both;
}
@keyframes fadeInLeft {
  from {
    opacity: 0;
    -webkit-transform: translate3d(-100%, 0, 0);
    transform: translate3d(-100%, 0, 0);
  }

  to {
    opacity: 1;
    -webkit-transform: translate3d(0, 0, 0);
    transform: translate3d(0, 0, 0);
  }
}
</style>
