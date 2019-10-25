<template>
  <v-app id="inspire">
    <v-navigation-drawer v-model="drawer" app clipped>
      <v-list dense>
        <v-list-item>
          <v-list-item-action>
            <v-icon>mdi-view-dashboard</v-icon>
          </v-list-item-action>
          <v-list-item-content>
            <v-list-item-title>Dashboard</v-list-item-title>
          </v-list-item-content>
        </v-list-item>
        <v-list-item>
          <v-list-item-action>
            <v-icon>mdi-settings</v-icon>
          </v-list-item-action>
          <v-list-item-content>
            <v-list-item-title>Settings</v-list-item-title>
          </v-list-item-content>
        </v-list-item>
      </v-list>
    </v-navigation-drawer>

    <v-app-bar app clipped-left>
      <v-app-bar-nav-icon @click.stop="drawer = !drawer"></v-app-bar-nav-icon>
      <v-toolbar-title>Application</v-toolbar-title>
    </v-app-bar>

    <v-content>
      <v-container class="fill-height" fluid>
        <v-row align="center" justify="center">
          <v-col class="shrink">
            <game-canvas style="width: 100%; height: 600px;">
              <snake
                v-for="(player, index) of players"
                :key="`${index}`"
                :x1="((index / players.length) * 100)"
                :x2="((index / players.length) * 100) + (100 / players.length)"
                :y1="100"
                :y2="100"
                :color="player.color"
                :value="player.position"
                :tick="tick"
                :direction="player.direction"
                />
            </game-canvas>
          </v-col>
        </v-row>
      </v-container>
    </v-content>

    <v-footer app>
      <span>&copy; 2019</span>
    </v-footer>
  </v-app>
</template>

<script>
import GameCanvas from './components/GameCanvas'
import Snake from './components/Snake'
import Direction from './components/Direction.js'
export default {
  props: {
    source: String
  },
  data: () => ({
    drawer: null,
    tick: 0,
    players: [
      { position: 32, color: 'red', direction: Direction.UP }
    ]
  }),
  created () {
    this.$vuetify.theme.dark = true
  },
  components: {
    GameCanvas,
    Snake
  },
  // Randomly selects a value to randomly increment or decrement every 16 ms.
  // Not really important, just demonstrates that reactivity still works.
  mounted () {
    addEventListener('keydown', e => {
      const player = this.players[0]
      if (e.keyCode === 38) {
        player.direction = Direction.UP
      } else if (e.keyCode === 40) {
        player.direction = Direction.DOWN
      } else if (e.keyCode === 37) {
        player.direction = Direction.LEFT
      } else if (e.keyCode === 39) {
        player.direction = Direction.RIGHT
      }
      console.log('User Input: ' + player.direction)
    })
    setInterval(() => {
      this.tick++
      console.log(this.tick + ' : ' + this.players[0].direction)
    }, 5000)
  }
}
</script>
