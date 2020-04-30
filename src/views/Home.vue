<template>
  <div class="home">
    <div class="box" ref="wave"></div>
    <div v-for="(item, index) in sourse" :key="index"> 
        <vueAudio :url="item.url" @is-playing="isPalying" />
    </div>
  </div>
</template>

<script>
// @ is an alias to /src
import vueAudio from './basic/audio'
var WaveSurfer = require('wavesurfer.js')

export default {
  name: 'home',
  components: {
    vueAudio
  },
  data() {
    return {
      sourse: [{
        url: 'http://m801.music.126.net/20200430100425/7721838b7aae86fc6f0332f14cd3e3db/jdymusic/obj/w5zDlMODwrDDiGjCn8Ky/1908362929/b1a9/6af5/dacd/44330caef4591aad8e6b63b8c9220de2.mp3'
      },{
        url: 'http://m8.music.126.net/20200430100443/7782c031ee027956f679a9899a004c34/ymusic/560f/075e/535f/ae15cebedc04c39f79f6d5ac577f2977.mp3'
      }],
      url: '/assets/mp3/a.mp3',
      box: {}
    }
  },
  methods: {
    initWaveSurfer(){
      // eslint-disable-next-line no-console
      this.box = WaveSurfer.create({
         container: this.$refs.wave,
        //  waveColor: 'violet',
        //  progressColor: 'purple',
         waveColor: '#428bca',
         progressColor: '#31708f',
         height: 120,
         barWidth: 3
      })

      this.box.load(this.sourse[0].url);
    },
    isPalying(){

    }
  },
  mounted() {
    this.initWaveSurfer()
  }
}
</script>

<style scope>
  .box{
    width: 500px;
    height: 200px;
    display: inline-block;
  }
</style>
