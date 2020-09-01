<template>
    <div id="app">
        <button @click="init">点击</button>
        <image-preview
            ref="slider1"
            v-model="value"
            :showNumber="showNumber"
            :height="height"
            :width="width"
            :photosGap="photosGap"
            :photoList="photoUrlsArr"
            :sliderNum="sliderNum"
            @on-change="changeImage"
        ></image-preview>
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
import ImagePreview from "./components/ImagePreview.vue";

export default {
    name: "App",
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
                "/imgs/7.png",
                "/imgs/8.png",
                "/imgs/9.png",
                "/imgs/10.png",
                "/imgs/11.png",
                "/imgs/12.png",
                "/imgs/13.png",
                "/imgs/14.png",
                "/imgs/15.png",
                "/imgs/16.png",
                "/imgs/17.png",
                "/imgs/18.png",
                "/imgs/19.png",
                "/imgs/20.png",
                "/imgs/21.png",
                "/imgs/22.png",
                "/imgs/23.png",
                "/imgs/24.png",
                "/imgs/25.png",
                "/imgs/26.png",
                "/imgs/27.png",
                "/imgs/28.png",
                "/imgs/29.png",
                "/imgs/30.png",
            ],
            value: 30,
            width: 200,
            height: 400,
            showNumber: 3,
            photosGap: 10,
            sliderNum: 1,
        };
    },
    components: {
        ImagePreview,
    },
    methods: {
        changeImage(index) {},
        init(val) {
            this.$refs.slider1.init(29);
        }
    },
};
</script>