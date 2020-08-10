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
            photoWidth: {
                default: 300,
                validator: function(value) {
                    return value > 0
                }
            },
            photoHeight: {
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
            photoUrls: {
                type: Array,
                required: true
            },
            initPhotoIndex: {
                default: 0,
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
                    height: this.photoHeight + 'px'
                },
                showUrls: [],
                leftIndex: 0,
                rightIndex: 0,
                photoSliderStyle: {
                    width: this.showNumber * this.photoWidth + this.photosGap * (this.showNumber-1) + 'px',
                },
                photoStyle: {
                    width: this.photoWidth + 'px',
                    height: this.photoHeight + 'px',
                    marginRight: this.photosGap + 'px'
                },
                photoSliderLoccation: -(this.photosGap + this.photoWidth),  
            }
        },
        methods: {
            // 初始化展示数组 showUrls
            initShowUrls() {
                // 1. 初始化 leftIndex&rightIndex
                if (this.initPhotoIndex == 0) {
                    this.leftIndex = this.photoUrls.length-1
                } else {
                    this.leftIndex = this.initPhotoIndex-1
                }
                this.rightIndex = this.leftIndex + this.showNumber + 1
                if (this.rightIndex > this.photoUrls.length-1) {
                    this.rightIndex = this.rightIndex - (this.photoUrls.length-1) - 1
                }
                // 2. 根据leftIndex&rightIndex，到 photoUrls 截取 showUrls
                if (this.leftIndex < this.rightIndex) {
                    this.showUrls = this.photoUrls.slice(this.leftIndex, this.rightIndex+1)
                } else {
                    this.showUrls = this.photoUrls.slice(this.leftIndex).concat(this.photoUrls.slice(0, this.rightIndex+1))
                }
            },
            moveSlider(isLeft) {
                if (isLeft) {
                    let temp = this.showUrls[0]
                    this.showUrls = this.showUrls.slice(1).concat(temp)
                } else {
                    let temp = [this.showUrls[this.showUrls.length-1]]
                    this.showUrls = this.showUrls.slice(0, this.showUrls.length-1)
                    this.showUrls = temp.concat(this.showUrls)
                }
            },
            changeShowUrls(isLeft) {
                if (isLeft) {
                    // 1. 移动动画
                    // 2. 删除左边一个元素, 并同步 leftIndex
                    this.showUrls = this.showUrls.slice(1)
                    if (this.leftIndex == this.photoUrls.length-1) {
                        this.leftIndex = 0
                    } else {
                        this.leftIndex = this.leftIndex+1
                    }
                    // 3. 定位右边元素, 并同步 rightIndex
                    if (this.rightIndex == this.photoUrls.length-1) {
                        this.rightIndex = 0
                    } else {
                        this.rightIndex = this.rightIndex+1
                    }
                    // 4. 添加右边元素到 showUrls
                    this.showUrls = this.showUrls.concat(this.photoUrls[this.rightIndex])
                } else {
                    // 1. 移动动画
                    // 2. 删除右边一个元素, 并同步 rightIndex
                    this.showUrls = this.showUrls.slice(0, this.showUrls.length-1)
                    if (this.rightIndex == 0) {
                        this.rightIndex = this.photoUrls.length-1
                    } else {
                        this.rightIndex = this.rightIndex-1
                    }
                    // 3. 定位左边元素, 并同步 leftIndex
                    if (this.leftIndex == 0) {
                        this.leftIndex = this.photoUrls.length-1
                    } else {
                        this.leftIndex = this.leftIndex-1
                    }
                    // 4. 添加左边元素到 showUrls
                    this.showUrls = [this.photoUrls[this.leftIndex]].concat(this.showUrls)
                }
            }
        }
    }
</script>