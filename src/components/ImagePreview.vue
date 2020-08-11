<template>
  <div class="photo-preview" :style="idStyle">
    <div class="arrow arrow-left" @click="changeShowUrls(true)">
      <i class="iconfont icon-left"></i>
    </div>
    <div class="photo-slider-wrap" :style="photoSliderStyle">
      <div
        class="photo-slider"
        :style="{left: photoSliderLoccation+'px'}">
        <div
            class="photo"
            :style="photoStyle"
            v-for="photoUrl in showUrls"
            :key="photoUrl">
          <img :style="photoStyle" :src="photoUrl" />
        </div>
      </div>
    </div>
    <div class="arrow arrow-right" @click="changeShowUrls(false)">
      <i class="iconfont icon-right"></i>
    </div>
  </div>
</template>

<style lang="less" scoped>
.photo-preview {
  display: flex;
  flex-direction: row;
  .arrow {
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    width: 40px;
    height: 100%;
    cursor: pointer;
    box-shadow: 0 0.5px 3px rgba(0, 0, 0, 0.15);
    user-select: none;
  }
  .arrow:hover {
    box-shadow: 0 2px 5px rgba(0, 0, 0, 0.15);
  }
  .iconfont {
    font-size: 20px;
  }
  .photo-slider-wrap {
    position: relative;
    overflow: hidden;
    margin: 0 10px;
    .photo-slider {
      position: absolute;
      top: 0;
      display: flex;
      flex-direction: row;
      justify-content: center;
      align-items: center;
      overflow: hidden;
      transition-property: left;
      transition-timing-function: ease-in-out;
      transition-delay: 0;
    }
  }
}
</style>

<script>
    export default {
        name: 'ImagePreview',
        props: {
            showNumber: {
                default: 3,
                validator: function(value) {
                    return value > 0
                }
            },
            width: {
                default: 300,
                validator: function(value) {
                    return value > 0
                }
            },
            height: {
                default: 600,
                validator: function(value) {
                    return value > 0
                }
            },
            photosGap: {
                default: 10,
                validator: function(value) {
                    return value > 0
                }
            },
            photoList: {
                type: Array,
                required: true,
                validator: function(value) {
                    return value.length > 0
                }
            },
            value: {
                required: true,
                validator: function(value) {
                    return value >= 0
                }
            }
        },
        created() {
            // 初始化卡片展示数组 showUrls
            this.initShowUrls()
        },
        data() {
            return {
                idStyle: {
                    height: this.height + 'px'
                },
                showUrls: [],
                leftIndex: 0,
                rightIndex: 0,
                photoSliderStyle: {
                    width: this.showNumber * this.width + this.photosGap * (this.showNumber-1) + 'px',
                },
                photoStyle: {
                    width: this.width + 'px',
                    height: this.height + 'px',
                    marginRight: this.photosGap + 'px'
                },
                photoSliderLoccation: -(this.photosGap + this.width),  
            }
        },
        methods: {
            // 初始化展示数组 showUrls
            initShowUrls() {
                // 1. 初始化 leftIndex&rightIndex
                if (this.value === 0) {
                    this.leftIndex = this.photoList.length-2
                } else if (this.value === 1) {
                    this.leftIndex = this.photoList.length-1
                } else {
                    this.leftIndex = this.value-2
                }
                this.rightIndex = this.leftIndex + this.showNumber + 1
                if (this.rightIndex > this.photoList.length-1) {
                    this.rightIndex = this.rightIndex - (this.photoList.length-1) - 1
                }
                // 2. 根据leftIndex&rightIndex，到 photoList 截取 showUrls
                if (this.leftIndex < this.rightIndex) {
                    this.showUrls = this.photoList.slice(this.leftIndex, this.rightIndex+1)
                } else {
                    this.showUrls = this.photoList.slice(this.leftIndex).concat(this.photoList.slice(0, this.rightIndex+1))
                }
                console.log("init: ", this.value, ", left: ", this.leftIndex, ", right: ", this.rightIndex )
            },
            changeShowUrls(isLeft) {
                if (isLeft) {
                    // 1. 移动动画
                    // 2. 删除左边一个元素, 并同步 leftIndex
                    this.showUrls = this.showUrls.slice(1)
                    if (this.leftIndex === this.photoList.length-1) {
                        this.leftIndex = 0
                    } else {
                        this.leftIndex = this.leftIndex+1
                    }
                    // 3. 定位右边元素, 并同步 rightIndex
                    if (this.rightIndex === this.photoList.length-1) {
                        this.rightIndex = 0
                    } else {
                        this.rightIndex = this.rightIndex+1
                    }
                    // 4. 添加右边元素到 showUrls
                    this.showUrls = this.showUrls.concat(this.photoList[this.rightIndex])
                } else {
                    // 1. 移动动画
                    // 2. 删除右边一个元素, 并同步 rightIndex
                    this.showUrls = this.showUrls.slice(0, this.showUrls.length-1)
                    if (this.rightIndex === 0) {
                        this.rightIndex = this.photoList.length-1
                    } else {
                        this.rightIndex = this.rightIndex-1
                    }
                    // 3. 定位左边元素, 并同步 leftIndex
                    if (this.leftIndex === 0) {
                        this.leftIndex = this.photoList.length-1
                    } else {
                        this.leftIndex = this.leftIndex-1
                    }
                    // 4. 添加左边元素到 showUrls
                    this.showUrls = [this.photoList[this.leftIndex]].concat(this.showUrls)
                }
                if (this.leftIndex === this.photoList.length-1) {
                    this.$emit('on-change', 0)
                } else {
                    this.$emit('on-change', this.leftIndex+1)
                }
            }
        },
        computed: {
            _value: {
                get () {
                    return this.value
                },
                set (value) {
                    this.$emit('input', value)
                }
            }
        }
    }
</script>