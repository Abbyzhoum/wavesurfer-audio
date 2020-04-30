<template>
  <div>
    <div></div>
    <div>
      <div v-for="(index, item) in sourse" :key="index">
        <p class="wave-line">{{item.name}}</p>
      </div> 
    </div>
    
  </div>
</template>
<script>
export default {
  data(){
    return {
      sourse: [{
        name: '微微',
        url: 'http://m801.music.126.net/20200430100425/7721838b7aae86fc6f0332f14cd3e3db/jdymusic/obj/w5zDlMODwrDDiGjCn8Ky/1908362929/b1a9/6af5/dacd/44330caef4591aad8e6b63b8c9220de2.mp3'
      },{
        name: '大猪弟子',
        url: 'http://m8.music.126.net/20200430100443/7782c031ee027956f679a9899a004c34/ymusic/560f/075e/535f/ae15cebedc04c39f79f6d5ac577f2977.mp3'
      }],
      url: '/assets/mp3/a.mp3',
      box: {}
    }
  },
  methods: {

  }
}
</script>

<style scope>
  .wave-line {
    display: block;
    line-height: 1;
    white-space: nowrap;
    cursor: pointer;
    background: #fff;
    border: 1px solid #dcdfe6;
    border-color: #dcdfe6;
    color: #606266;
    -webkit-appearance: none;
    text-align: center;
    box-sizing: border-box;
    outline: none;
    margin: 0;
    transition: .1s;
    font-weight: 500;
    -moz-user-select: none;
    -webkit-user-select: none;
    -ms-user-select: none;
    padding: 12px 20px;
    font-size: 14px;
    border-radius: 4px;
}
</style>