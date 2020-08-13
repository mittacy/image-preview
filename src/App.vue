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
        "/imgs/5.png"
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