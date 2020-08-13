<template>
  <div id="app">
    <image-preview
      :showNumber="3"
      :width="200"
      :height="400"
      :photosGap="10"
      :value="1"
      :photoList="photoUrlsArr"
      @on-change="changeImage">
    </image-preview>
  </div>
</template>

<style>
#app {
  margin: 0 auto;
  padding: 0;
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
}
</style>

<script>
import ImagePreview from './components/ImagePreview.vue'

export default {
  name: 'App',
  data() {
    return {
      photoUrlsArr: [
        "/imgs/0.png",
        "/imgs/1.png",
        "/imgs/2.png",
        "/imgs/3.png",
        "/imgs/4.png",
        "/imgs/5.png",
        "/imgs/6.png",
        "https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1597216910876&di=2ee5452bda1632d88ecd6d0a40377495&imgtype=0&src=http%3A%2F%2Fc.hiphotos.baidu.com%2Fzhidao%2Fpic%2Fitem%2F500fd9f9d72a6059c7b2b8872a34349b023bba89.jpg",
        "https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1597216965274&di=8ad430e00a1657380a67b9f8d9ddb072&imgtype=0&src=http%3A%2F%2Fattachments.gfan.com%2Fforum%2Fattachments2%2F201303%2F23%2F124331t26mddrgddg56s2z.jpg",
        "https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1597216980051&di=a9361b9ecf7ba8e07668623336b1d565&imgtype=0&src=http%3A%2F%2Fcdn.duitang.com%2Fuploads%2Fitem%2F201408%2F05%2F20140805191039_cuTPn.jpeg",
        "https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1597216991300&di=2131877866cabf7b617647d29d7abd0e&imgtype=0&src=http%3A%2F%2Fd.paper.i4.cn%2Fmax%2F2016%2F12%2F01%2F10%2F1480558471363_838559.jpg",
        "https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1597217007426&di=eeec39dccec29ca31f940c930d5b1738&imgtype=0&src=http%3A%2F%2Fcdn.duitang.com%2Fuploads%2Fitem%2F201412%2F27%2F20141227150432_FeS8P.jpeg",
        "https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1597217019478&di=1201c6b88a906e3cb9f90f2f90ff1608&imgtype=0&src=http%3A%2F%2Fattachments.gfan.com%2Fforum%2F201412%2F13%2F214009e1eu8us2buyx9l0y.jpg",
        "https://ss0.bdstatic.com/70cFuHSh_Q1YnxGkpoWK1HF6hhy/it/u=2057763469,3313822915&fm=26&gp=0.jpg"
      ]
    }
  },
  components: {
    ImagePreview
  },
  methods: {
    changeImage(index) {
      console.log("index: ", index)
    }
  }
}
</script>