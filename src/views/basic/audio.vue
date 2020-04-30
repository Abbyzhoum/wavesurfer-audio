<template>
  <div class="di main-wrap" v-loading="audio.waiting">
     <!-- 这里设置了ref属性后，在vue组件中，就可以用this.$refs.audio来访问该dom元素 -->
     <audio ref="audio" class="dn" 
        :src="url"
        :preload="audio.preload"
        @play="onPlay" 
        @error="onError"
        @waiting="onWaiting"
        @pause="onPause" 
        @timeupdate="onTimeupdate" 
        @loadedmetadata="onLoadedmetadata"
        ></audio>
        <div>
        <el-button type="text" @click="startPlayOrPause">{{audio.playing | transPlayPause}}</el-button>
        <el-button v-show="!controlList.noSpeed" type="text" @click="changeSpeed">{{audio.speed | transSpeed}}</el-button>

        <el-tag type="info">{{ audio.currentTime | formatSecond}}</el-tag>

        <el-slider v-show="!controlList.noProcess" v-model="sliderTime" :format-tooltip="formatProcessToolTip" @change="changeCurrentTime" class="slider"></el-slider>

        <el-tag type="info">{{ audio.maxTime | formatSecond }}</el-tag>

        <!-- <el-button v-show="!controlList.noMuted" type="text" @click="startMutedOrNot">{{audio.muted | transMutedOrNot}}</el-button> -->

        <!-- <el-slider v-show="!controlList.noVolume" v-model="volume" :format-tooltip="formatVolumeToolTip" @change="changeVolume" class="slider"></el-slider> -->

        <!-- <a ref="url"  @click="downloadAudio" :href="url" target="_blank" class="download">下载</a> -->
        <el-button @click="downloadAudio" class="download" type="text">下载</el-button>
      </div>
    </div>
</template>

<script>
  function realFormatSecond(second) {
    var secondType = typeof second
    if (secondType === 'number' || secondType === 'string') {
      second = parseInt(second)
      var hours = Math.floor(second / 3600)
      second = second - hours * 3600
      var mimute = Math.floor(second / 60)
      second = second - mimute * 60
      return hours + ':' + ('0' + mimute).slice(-2) + ':' + ('0' + second).slice(-2)
    } else {
      return '0:00:00'
    }
  }


export default {
  name: 'VueAudio',
  data() {
    return {
      vox: [],
      audio: {
          currentTime: 0,
          maxTime: 0,
          playing: false,
          muted: false,
          speed: 1,
          waiting: true,
          preload: 'auto'
        },
        sliderTime: 0,
        volume: 100,
        speeds: [1, 1.5, 2],
        controlList: {
          // 不显示下载
          // noDownload: false,
          // 不显示静音
          // noMuted: false,
          // 不显示音量条
          noVolume: false,
          // 不显示进度条
          noProcess: false,
          // 只能播放一个
          onlyOnePlaying: false,
          // 不要快进按钮
          noSpeed: false
        },
        control: 'noDownload noMuted onlyOnePlaying'
    }
  },
  methods: {
      setControlList () {
        let controlList = this.control.split(' ')
        controlList.forEach((item) => {
          if(this.controlList[item] !== undefined){
            this.controlList[item] = true
          }
        })
      },
      changeSpeed() {
        let index = this.speeds.indexOf(this.audio.speed) + 1
        this.audio.speed = this.speeds[index % this.speeds.length]
        this.$refs.audio.playbackRate = this.audio.speed
      },
      startMutedOrNot() {
        this.$refs.audio.muted = !this.$refs.audio.muted
        this.audio.muted = this.$refs.audio[0].muted
      },
      // 音量条toolTip
      // formatVolumeToolTip(index) {
      //   return '音量条: ' + index
      // },
      // 进度条toolTip
      formatProcessToolTip(index = 0) {
        index = parseInt(this.audio.maxTime / 100 * index)
        return '进度条: ' + realFormatSecond(index)
      },
      // 音量改变
      // changeVolume(index = 0) {
      //   this.$refs.audio.volume = index / 100
      //   this.volume = index
      // },
      // 播放跳转
      changeCurrentTime(index) {
        this.$refs.audio.currentTime = parseInt(index / 100 * this.audio.maxTime)
      },
      startPlayOrPause() {
        return this.audio.playing ? this.pausePlay() : this.startPlay()
      },
      // 开始播放
      startPlay() {
        // console.log(this.$refs.audio[0])
        this.$refs.audio.play()
      },
      // 暂停
      pausePlay() {
        this.$refs.audio.pause()
      },
      // 当音频暂停
      onPause () {
        this.audio.playing = false
      },
      // 当发生错误, 就出现loading状态
      onError () {
        this.audio.waiting = true
      },
      // 当音频开始等待
      onWaiting (res) {
        // eslint-disable-next-line no-console
        console.log(res)
      },
      // 当音频开始播放
      onPlay (res) {
        this.audio.playing = true
        this.audio.loading = false
        // if(!this.controlList.onlyOnePlaying){
        //   return 
        // }
        let target = res.target
        let audios = document.getElementsByTagName('audio');
        [...audios].forEach((item) => {
          if(item !== target){
            item.pause()
          }
        })

        this.emitWhichAudio()
      },
      // 当timeupdate事件大概每秒一次，用来更新音频流的当前播放时间
      onTimeupdate(res) {
        this.audio.currentTime = res.target.currentTime
        this.sliderTime = parseInt(this.audio.currentTime / this.audio.maxTime * 100)
      },
      // 当加载语音流元数据完成后，会触发该事件的回调函数
      // 语音元数据主要是语音的长度之类的数据
      onLoadedmetadata(res) {
        // eslint-disable-next-line no-console
        console.log('loadedmetadata')
        this.audio.waiting = false
        this.audio.maxTime = parseInt(res.target.duration)
      },
      downloadAudio() {
        fetch(this.url).then(res => res.blob()).then(blob => {
          const a = document.createElement('a')
          document.body.appendChild(a)
          a.style.display = 'none'
          const url = window.URL.createObjectURL(blob)
          a.href = url
          // 指定下载的文件名
          // a.download = this.downloadUrl
          a.download = this.url
          a.click();
          document.body.removeChild(a)
          window.URL.revokeObjectURL(url)
        })
      },
      emitWhichAudio(){
        this.$emit('is-playing',{
          url: this.url
        })
      }
  },
  mounted() {
    // this.changeBasic()
  },
  created() {
    this.setControlList()
  },
  props:{
    url: {
      type: String,
      required: true
    }
    // downloadUrl: {
    //   type: String,
    //   required: true
    // }
  },
   filters: {
      formatSecond(second = 0) {
        return realFormatSecond(second)
      },
      transPlayPause(value) {
        return value ? '暂停' : '播放'
      },
      // transMutedOrNot(value) {
      //   return value ? '放音' : '静音'
      // },
      transSpeed(value) {
        return '快进: x' + value
      }
    }
}
</script>

<style scoped>
  .main-wrap{
    padding: 10px 15px;
    border: 1px solid lightblue;
  }
  .slider {
    display: inline-block;
    width: 100px;
    position: relative;
    top: 14px;
    margin-left: 15px;
  }
  .di {
    display: inline-block;
    margin-top: 5px;
  }
  .download {
    color: #409EFF;
    margin-left: 15px;
  }
  .dn{
    display: none;
  }
</style>