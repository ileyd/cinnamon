<template>
    <v-layout row justify-center>
        <v-dialog v-model="dialog" fullscreen transition="dialog-bottom-transition" :overlay=false>
            <v-btn primary dark slot="activator">Open Internal Player</v-btn>
            <v-card>
                <v-toolbar dark class="primary">
                    <v-btn icon @click.native="dialog = false" dark>
                        <v-icon>close</v-icon>
                    </v-btn>
                    <v-toolbar-title>Player</v-toolbar-title>
                </v-toolbar>
                <div class="video"></div>
            </v-card>
        </v-dialog>
    </v-layout>
</template>


<script>/* eslint-disable indent */
import WebTorrent from 'webtorrent'
import { mapGetters } from 'vuex'
export default {
  name: 'Player',
  data () {
    return {
      title: 'Player',
      mpv: null,
      dialog: false,
        playerOptions: {
          // videojs options
          muted: true,
          language: 'en',
          width: 1000,
          playbackRates: [0.7, 1.0, 1.5, 2.0],
          sources: {
            type: 'video/mp4',
            src: ''
          }
        }
    }
  },
  computed: mapGetters([
    'filePath',
    'magnetURI'
  ]),
  mounted () {
    // console.log('this is current videojs instance object', this.myVideoPlayer)
    this.stream(this.magnetURI)
  },
  methods: {
    stream: function (magnetURI) {
      let client = new WebTorrent()
      console.log(magnetURI)
      client.add(magnetURI, {path: '/tmp/'}, function (torrent) {
        let file = torrent.files.find(function (file) {
          return file.name.endsWith('.mp4')
        })
        if (file) {
          console.log('compatible mp4 file found')
          file.appendTo('.video')
        } else {
          console.log('no compatible mp4 file')
        }
        console.log(file)
        torrent.on('done', function () {
          console.log('torrent download finished')
        })
      })
    },
    // listen event
    onPlayerPlay (player) {
      console.log('player play!', player)
    },
    onPlayerPause (player) {
      console.log('player pause!', player)
    },
    // ...player event

    // or listen state event
    playerStateChanged (playerCurrentState) {
      console.log('player current update state', playerCurrentState)
    },

    // player is ready
    playerReadied (player) {
      console.log('the player is readied', player)
        // you can use it to do something...
        // player.[methods]
    }
  }
}
</script>

<style scoped="">

</style>
