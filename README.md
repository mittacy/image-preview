# image-preview

## props

| 名称       | 作用                                    | 是否必要 | 默认值 | 范围                       |
| ---------- | --------------------------------------- | -------- | ------ | -------------------------- |
| showNumber | 展示卡片数量                            |          | 3      | 2<=val<photoUrls.length    |
| width      | 每张卡片的宽度                          |          | 200    | >0                         |
| height     | 每张卡片的高度                          |          | 400    | >0                         |
| photosGap  | 两张卡片之间的间隙                      |          | 10     | >0                         |
| value      | 初始化卡片索引(即加载时出现的img是哪张) |          | 2      | 1<=val<=photoUrls.length-2 |
| photoList  | 卡片地址数组                            | 是       |        |                            |

## methods

### on-change Function

```vue
<image-preview ...props @on-change="changeImage"></image-preview>

<script>
  function changeImage(index) {
    console.log(index);
  }
</script>
```

