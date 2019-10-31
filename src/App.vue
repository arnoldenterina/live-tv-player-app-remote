<template>
  <v-app id="inspire">
    <v-navigation-drawer
      v-model="drawer"
      :clipped="$vuetify.breakpoint.lgAndUp"
      app
      :width="300"
      :mini-variant.sync="mini"
    >
      <v-list-item>
        <v-list-item-icon>
            <v-icon>mdi-youtube-tv</v-icon>
          </v-list-item-icon>
        <v-list-item-title>List Channels</v-list-item-title>

        <v-btn
          icon
          @click.stop="mini = !mini"
        >
          <v-icon>mdi-chevron-left</v-icon>
        </v-btn>
      </v-list-item>

      <v-divider></v-divider>
      <v-list dense >
        <v-skeleton-loader
          :loading="loading"
          transition="fade-transition"
          height="94"
          type="list-item-avatar"
        >
          <v-list-item-group v-model="channel">
            <v-list-item v-for="(c, index) in channels" :key="`c-${index}`"
            link @click="playVideo(c.src)" :title="c.title"
            >
              <v-list-item-avatar tile>
                <v-img :src="c.img"></v-img>
              </v-list-item-avatar>

              <v-list-item-content>
                <v-list-item-title>{{c.title}}</v-list-item-title>
              </v-list-item-content>
            </v-list-item>
          </v-list-item-group>
        </v-skeleton-loader>
      </v-list>
    </v-navigation-drawer>

    <v-app-bar
      :clipped-left="$vuetify.breakpoint.lgAndUp"
      app
      dark
      color="red"
      dense
    >
      <v-toolbar-title
        style="width: 300px"
      >
        <v-app-bar-nav-icon @click.stop="drawer = !drawer"></v-app-bar-nav-icon>
        <v-icon class="mx-4">mdi-youtube</v-icon>
        <span class="hidden-sm-and-down">ARLive TV</span>
      </v-toolbar-title>
      <v-spacer></v-spacer>
      <v-btn
        icon
        large
      >
        <v-avatar
          size="32px"
          item
        >
          <v-img
            src="@/assets/ARMUSIC_LOGO.png"
            alt="LiveTV"
          >
          </v-img></v-avatar>
      </v-btn>
    </v-app-bar>
    <v-content>
      <v-container fluid grid-list-lg fill-height>
        <v-row  no-gutters>
          <v-col>
            <video-player ref="videoPlayer"
                          class="vjs-custom-skin"
                          :options="playerOptions"
                          @ready="onPlayerReady($event)">
            </video-player>
          </v-col>
        </v-row>
      </v-container>
    </v-content>
  </v-app>
</template>

<script>
  import {videoPlayer} from 'vue-videojs7'
  const axios = require('axios');

  export default {
    props: {
      source: String,
    },
    components: {
      videoPlayer
    },
    data: () => ({
      drawer: null,
      loading: true,
      mini: false,
      channel: -1,
      auth: '',
      channels: [
        {
          src: "http://peer3.savitar.tv/AE/myStream/playlist.m3u8?wmsAuthSign=",
          img: "http://ustvgo.tv/wp-content/uploads/2019/01/AE.png",
          title: "A&E"
        },
        {
          src: "http://peer3.savitar.tv/ABC/myStream/playlist.m3u8?wmsAuthSign=",
          img: "http://ustvgo.tv/wp-content/uploads/2018/10/abc-269x151.jpg",
          title: "ABC"
        },
        {
          src: "http://peer3.savitar.tv/AMC/myStream/playlist.m3u8?wmsAuthSign=",
          img: "http://ustvgo.tv/wp-content/uploads/2019/01/AMC-1.png",
          title: "AMC"
        },
        {
          src: "http://peer3.savitar.tv/Animal/myStream/playlist.m3u8?wmsAuthSign=",
          img: "http://ustvgo.tv/wp-content/uploads/2019/01/animal-planet.png",
          title: "Animal Planet"
        },
        {
          src: "http://peer3.savitar.tv/Boomerang/myStream/playlist.m3u8?wmsAuthSign=",
          img: "http://ustvgo.tv/wp-content/uploads/2019/08/Boomerang.png",
          title: "Boomerang"
        },
        {
          src: "http://peer3.savitar.tv/CN/myStream/playlist.m3u8?wmsAuthSign=",
          img: "http://ustvgo.tv/wp-content/uploads/2019/01/cartoon-network.jpg",
          title: "Cartoon Network"
        },
        {
          src: "http://peer3.savitar.tv/CNN/myStream/playlist.m3u8?wmsAuthSign=",
          img: "http://ustvgo.tv/wp-content/uploads/2018/10/CNN-1.png",
          title: "CNN"
        },
        {
          src: "http://peer3.savitar.tv/Comedy/myStream/playlist.m3u8?wmsAuthSign=",
          img: "http://ustvgo.tv/wp-content/uploads/2019/01/comedy-central-269x151.png",
          title: "Comedy Central"
        },
        {
          src: "http://peer3.savitar.tv/Discovery/myStream/playlist.m3u8?wmsAuthSign=",
          img: "http://ustvgo.tv/wp-content/uploads/2019/01/Discovery.png",
          title: "Discovery Channel"
        },
        {
          src: "http://peer3.savitar.tv/Disney/myStream/playlist.m3u8?wmsAuthSign=",
          img: "http://ustvgo.tv/wp-content/uploads/2019/01/disney-269x151.png",
          title: "Disney Channel"
        },
        {
          src: "http://peer3.savitar.tv/DisneyJr/myStream/playlist.m3u8?wmsAuthSign=",
          img: "http://ustvgo.tv/wp-content/uploads/2019/08/disney-jr-768x432-1.png",
          title: "DisneyJr"
        },
        {
          src: "http://peer3.savitar.tv/DisneyXD/myStream/playlist.m3u8?wmsAuthSign=",
          img: "http://ustvgo.tv/wp-content/uploads/2019/08/disney-xd-768x432-1.png",
          title: "DisneyXD"
        },
        {
          src: "http://peer3.savitar.tv/DIY/myStream/playlist.m3u8?wmsAuthSign=",
          img: "http://ustvgo.tv/wp-content/uploads/2019/08/diy.png",
          title: "DIY"
        },
        {
          src: "http://peer3.savitar.tv/ESPN/myStream/playlist.m3u8?wmsAuthSign=",
          img: "http://ustvgo.tv/wp-content/uploads/2019/01/espn-269x151.png",
          title: "ESPN"
        },
        {
          src: "http://peer3.savitar.tv/ESPN2/myStream/playlist.m3u8?wmsAuthSign=",
          img: "http://ustvgo.tv/wp-content/uploads/2019/08/espn2-269x151.png",
          title: "ESPN 2"
        },
        {
          src: "http://peer3.savitar.tv/FoodNetwork/myStream/playlist.m3u8?wmsAuthSign=",
          img: "http://ustvgo.tv/wp-content/uploads/2019/01/food-network-269x151.png",
          title: "Food Network"
        },
        {
          src: "http://peer3.savitar.tv/FOX/myStream/playlist.m3u8?wmsAuthSign=",
          img: "http://ustvgo.tv/wp-content/uploads/2018/10/FOX-1.png",
          title: "Fox"
        },
        {
          src: "http://peer3.savitar.tv/FoxNews/myStream/playlist.m3u8?wmsAuthSign=",
          img: "http://ustvgo.tv/wp-content/uploads/2018/10/foxnews.jpg",
          title: "Fox News"
        },
        {
          src: "http://peer3.savitar.tv/FoxBusiness/myStream/playlist.m3u8?wmsAuthSign=",
          img: "http://ustvgo.tv/wp-content/uploads/2018/10/foxbusiness.jpg",
          title: "Fox Business"
        },
        {
          src: "http://peer3.savitar.tv/FS1/myStream/playlist.m3u8?wmsAuthSign=",
          img: "http://ustvgo.tv/wp-content/uploads/2019/01/fs1-269x151.png",
          title: "Fox Sports 1"
        },
        {
          src: "http://peer3.savitar.tv/FS2/myStream/playlist.m3u8?wmsAuthSign=",
          img: "http://ustvgo.tv/wp-content/uploads/2019/01/fs2-269x151.png",
          title: "Fox Sports 2"
        },
        {
          src: "http://peer3.savitar.tv/HBO/myStream/playlist.m3u8?wmsAuthSign=",
          img: "http://ustvgo.tv/wp-content/uploads/2019/01/hbo-269x151.png",
          title: "HBO"
        },
        {
          src: "http://peer3.savitar.tv/HMM/myStream/playlist.m3u8?wmsAuthSign=",
          img: "http://ustvgo.tv/wp-content/uploads/2019/01/HMM_logo_black-700x245.jpg",
          title: "Hallmark Movies & Mysteries"
        },
        {
          src: "http://peer3.savitar.tv/History/myStream/playlist.m3u8?wmsAuthSign=",
          img: "http://ustvgo.tv/wp-content/uploads/2019/01/History.png",
          title: "History Channel"
        },
        {
          src: "http://peer3.savitar.tv/LifetimeM/myStream/playlist.m3u8?wmsAuthSign=",
          img: "http://ustvgo.tv/wp-content/uploads/2019/08/lifetimeM.jpeg",
          title: "Lifetime Movies"
        },
        {
          src: "http://peer3.savitar.tv/MTV/myStream/playlist.m3u8?wmsAuthSign=",
          img: "http://ustvgo.tv/wp-content/uploads/2019/08/mtv.jpg",
          title: "MTV"
        },
        {
          src: "http://peer3.savitar.tv/NatGEOWild/myStream/playlist.m3u8?wmsAuthSign=",
          img: "http://ustvgo.tv/wp-content/uploads/2019/01/NatGeoWild.jpeg",
          title: "NAT GEO WILD"
        },
        {
          src: "http://peer3.savitar.tv/NatGEO/myStream/playlist.m3u8?wmsAuthSign=",
          img: "http://ustvgo.tv/wp-content/uploads/2019/01/National-Geographic-269x151.png",
          title: "National Geographic"
        },
        {
          src: "http://peer3.savitar.tv/NBA/myStream/playlist.m3u8?wmsAuthSign=",
          img: "http://ustvgo.tv/wp-content/uploads/2019/01/nbatv-269x151.jpg",
          title: "NBA TV"
        },
        {
          src: "http://peer3.savitar.tv/NBC/myStream/playlist.m3u8?wmsAuthSign=",
          img: "http://ustvgo.tv/wp-content/uploads/2018/10/nbc-logo.jpg",
          title: "NBC"
        },
        {
          src: "http://peer3.savitar.tv/NBCSN/myStream/playlist.m3u8?wmsAuthSign=",
          img: "http://ustvgo.tv/wp-content/uploads/2019/01/nbcsn-269x151.jpg",
          title: "NBC Sports"
        },
        {
          src: "http://peer3.savitar.tv/Nicktoons/myStream/playlist.m3u8?wmsAuthSign=",
          img: "http://ustvgo.tv/wp-content/uploads/2019/08/nicktoons.png",
          title: "Nicktoons"
        },
        {
          src: "http://peer3.savitar.tv/Nickelodeon/myStream/playlist.m3u8?wmsAuthSign=",
          img: "http://ustvgo.tv/wp-content/uploads/2019/01/Nickelodeon_2009.png",
          title: "Nickelodeon"
        },
        {
          src: "http://peer3.savitar.tv/Paramount/myStream/playlist.m3u8?wmsAuthSign=",
          img: "http://ustvgo.tv/wp-content/uploads/2019/08/paramount.jpg",
          title: "Paramount Network"
        },
        {
          src: "http://peer3.savitar.tv/CW/myStream/playlist.m3u8?wmsAuthSign=",
          img: "http://ustvgo.tv/wp-content/uploads/2019/01/cw-269x151.png",
          title: "The CW"
        }
      ],
      playerOptions: {
        autoplay: true,
        controls: true,
        controlBar: {
          timeDivider: false,
          durationDisplay: false
        }
      }
    }),
    computed: {
      player () {
        return this.$refs.videoPlayer.player
      }
    },
    methods: {
      onPlayerReady (player) {
        this.player.play()
      },
      playVideo: function (source) {
        console.log("channel", this.channel)
        const video = {
          withCredentials: false,
          type: 'application/x-mpegurl',
          src: source + this.auth
        }
        this.player.reset() // in IE11 (mode IE10) direct usage of src() when <src> is already set, generated errors,
        this.player.src(video)
        this.player.play()
      },
      getAuth(){
        axios.get('http://arlivetv.cf/getauth.php').then(({data}) => {
          this.auth = data.auth
          this.loading = false
        })
      }
    },
    mounted () {
      // this.playVideo(this.channels[0].src)
      this.getAuth()
    }
  }
</script>
<style scoped>
  .player {
    position: absolute !important;
    width: 50vw;
    height: 100%;
  }
  .vjs-custom-skin {
    height: 90vh !important;
  }

  .vjs-custom-skin /deep/ .video-js {
    height: 100%;
    width: 100%;
  }

  .v-navigation-drawer .v-list{
    height: calc(100vh - 100px);
    overflow-x: auto;
  }

  .v-navigation-drawer .v-list::-webkit-scrollbar{
    width: 10px;
    background-color: #F5F5F5;
  }
  .v-navigation-drawer .v-list::-webkit-scrollbar-track{
    -webkit-box-shadow: inset 0 0 6px rgba(0,0,0,0.3);
    border-radius: 8px;
    background-color: #F5F5F5;
  }
  .v-navigation-drawer .v-list::-webkit-scrollbar-thumb{
    border-radius: 8px;
    -webkit-box-shadow: inset 0 0 6px rgba(0,0,0,.3);
    background-color: #555;
  }
</style>
<style>
  .v-application--wrap .v-navigation-drawer__content{
    overflow: hidden;
  }
    body::-webkit-scrollbar, .v-navigation-drawer[data-booted=true]::-webkit-scrollbar {
      width: 10px;
      background-color: #F5F5F5;
    }

    body::-webkit-scrollbar-track, .v-navigation-drawer[data-booted=true]::-webkit-scrollbar-track {
      -webkit-box-shadow: inset 0 0 6px rgba(0,0,0,0.3);
      border-radius: 8px;
      background-color: #F5F5F5;
    }

    body::-webkit-scrollbar-thumb, .v-navigation-drawer[data-booted=true]::-webkit-scrollbar-thumb  {
      border-radius: 8px;
      -webkit-box-shadow: inset 0 0 6px rgba(0,0,0,.3);
      background-color: #555;
    }
</style>
