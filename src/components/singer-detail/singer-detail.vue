/**
 * 歌手详情页
 * @type {drl}
 */
<template>
  <transition name="slide">
    <music-list :title="title" :bg-image="bgImage" :songs="songs"></music-list>
  </transition>
</template>

<script type="text/ecmascript-6">
  import MusicList from 'components/music-list/music-list'
  import {getSingerDetail} from 'api/singer'
  import {ERR_OK} from 'api/config'
  import {createSong} from 'common/js/song'
  import {mapGetters} from 'vuex' // 为取数据提供语法糖

  export default { // 取数据，mapGetters方法---取数据语法糖
    computed: {
      title() {
        return this.singer.name
      },
      bgImage() {
        return this.singer.avatar
      },
      ...mapGetters([ // singer--->对应的是store->getters下的singer属性,一个数组可以map多个数据
        'singer'
      ])
    },
    data() {
      return {
        songs: []
      }
    },
    created() { // 组件created
      console.log(this.singer)
      this._getDetail() // 调用getSingerDetail方法
    },
    methods: {
      _getDetail() {
        if (!this.singer.id) { // 没有ID的时候，我们就回退到歌手列表
          this.$router.push('/singer')
          return
        }
        getSingerDetail(this.singer.id).then((res) => {
          if (res.code === ERR_OK) {
            this.songs = this._normalizeSongs(res.data.list)
            console.log(this.songs)
          }
        })
      },
      _normalizeSongs(list) { // 遍历歌手具体数据，对类做一些处理
        let ret = []
        list.forEach((item) => {
          let {musicData} = item // musicData适用于排行榜和歌单，面向对象结构赋值
          if (musicData.songid && musicData.albummid) { // songid\albummid很重要
            ret.push(createSong(musicData))
          }
        })
        return ret
      }
    },
    components: {
      MusicList
    }
  }
</script>

<style scoped lang="stylus" rel="stylesheet/stylus">
  .slide-enter-active, .slide-leave-active // 过渡动画
    transition: all 0.3s

  .slide-enter, .slide-leave-to
    transform: translate3d(100%, 0, 0)
</style>
