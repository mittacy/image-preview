# image-preview

### 展示

![展示图片]()

### 使用

1. 将 components 目录下的 ImagePreview 文件复制到项目的 components 目录或其他位置

2. 在需要引入该组件的 vue 文件中导入

   ~~~vue
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
   
   <script>
   import ImagePreview from './components/ImagePreview.vue';
   
   export default {
     data() {
       return {
         // 图片数组
         photoUrlsArr: [
           "/imgs/1.png",
           "/imgs/2.png",
           "/imgs/3.png",
           "/imgs/4.png",
           "/imgs/5.png",
           "/imgs/6.png"
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
   ~~~

### props

| 名称       | 作用                                    | 是否必要 | 默认值 | 范围                       |
| ---------- | --------------------------------------- | -------- | ------ | -------------------------- |
| showNumber | 展示卡片数量                            |          | 3      | 2<=val<photoUrls.length    |
| width      | 每张卡片的宽度                          |          | 200    | >0                         |
| height     | 每张卡片的高度                          |          | 400    | >0                         |
| photosGap  | 两张卡片之间的间隙                      |          | 10     | >0                         |
| value      | 初始化卡片索引(即加载时出现的img是哪张) |          | 2      | 1<=val<=photoUrls.length-2 |
| photoList  | 卡片地址数组                            | 是       |        |                            |

### methods

on-change Function

```vue
<image-preview ...props @on-change="changeImage"></image-preview>

<script>
  function changeImage(index) {
    console.log(index);
  }
</script>
```

