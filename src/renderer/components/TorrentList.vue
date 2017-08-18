<template>
    <v-layout justify-center align-center row wrap>
        <v-flex xs12>
            <div class="text-xs-center">
                <img src="https://nyaa.pantsu.cat/img/logo.png" alt="Pantsu Logo" class="mb-5"/>
            </div>
            <v-card>
                <v-toolbar>
                    <v-toolbar-title>Torrents</v-toolbar-title>
                </v-toolbar>
                <div class="text-xs-center" v-if="online">
                <v-progress-circular
                        v-bind:indeterminate="!online"
                        v-bind:active="!online"
                        class="primary--text"
                ></v-progress-circular>
                </div>
                <v-list two-line>
                    <v-list-tile v-for="item in torrents" v-bind:key="item.id">
                        <v-list-tile-content>
                            <v-list-tile-title>{{item.name}}</v-list-tile-title>
                            <v-list-tile-sub-title><span class="green--text text--lighten-1">S: {{item.seeders}}</span> <span class="red--text text--lighten-1 ">L: {{item.leechers}}</span>
                            </v-list-tile-sub-title>
                        </v-list-tile-content>

                        <v-list-tile-action>
                            <v-spacer></v-spacer>
                            <v-dialog width="800px">
                                <v-btn icon ripple dark slot="activator">
                                    <v-icon class="grey--text text--lighten-1">info</v-icon>
                                </v-btn>
                                <v-card>
                                    <v-card-title>
                                        <span class="headline">More info</span>
                                        <v-list-tile-action>
                                            <v-spacer></v-spacer>
                                            <!--<v-btn icon ripple v-clipboard="item.torrent" @click="snackbar = true">
                                                <v-icon class="gray--text text--lighten-2">content_copy</v-icon>
                                            </v-btn>-->
                                        </v-list-tile-action>
                                    </v-card-title>
                                    <v-card-text class="disabled" v-html="item.description"></v-card-text>
                                </v-card>
                                <v-snackbar
                                        :success="true"
                                        v-model="snackbar">Successfully copied torrent link
                                    <v-btn dark flat @click="snackbar = false">Close</v-btn>
                                </v-snackbar>
                            </v-dialog>
                            <v-spacer></v-spacer>
                        </v-list-tile-action>
                        <v-list-tile-action>
                            <v-spacer></v-spacer>
                                <v-btn icon ripple dark slot="activator" @click.native="setMagnetURI(item.magnet)" to="player">
                                    <v-icon class="purple--text text--lighten-2">play_circle_filled</v-icon>
                                </v-btn>
                            <v-spacer></v-spacer>
                        </v-list-tile-action>
                    </v-list-tile>
                </v-list>
            </v-card>
        </v-flex>
    </v-layout>
</template>

<script>
/* eslint-disable indent */
import pantsu from 'pantsu-api'
import { mapGetters } from 'vuex'
export default {
  name: 'TorrentList',
  data () {
    return {
        torrents: [],
        snackbar: false
    }
  },
  computed: mapGetters([
    'query',
    'limit',
    'online'
  ]),
  mounted () {
      pantsu.search(this.query, this.limit).then((data) => {
          this.torrents = data.torrents
          this.$store.commit('setOnline', true)
      }).catch((err) => {
          console.log(err)
          this.$store.commit('setOnline', false)
      })
  },
  methods: {
      setMagnetURI: function (data) {
          this.$store.commit('setMagnetURI', data)
      }
  }
}
</script>

<style scoped="">
    .disabled {
        pointer-events: none;
        cursor: default;
    }
</style>
