<template>
    <div class="photo-preview" :style="idStyle">
        <div
            class="arrow"
            :class="{'arrow-unactive': leftUnActive}"
            @click="changeShowUrls(true)">
            <i class="iconfont icon-left"></i>
        </div>
        <div
            class="photo-slider-wrap"
            :style="{width: photoSliderWidth + 'px'}">
            <div
                class="photo-slider"
                :style="{left: photoSliderLocation + 'px'}">
                <div
                    class="photo"
                    :style="photoStyle"
                    v-for="photoUrl in showUrls"
                    :key="photoUrl">
                    <img :style="photoStyle" :src="photoUrl" />
                </div>
            </div>
        </div>
        <div
            class="arrow"
            :class="{'arrow-unactive': rightUnActive}"
            @click="changeShowUrls(false)">
            <i class="iconfont icon-right"></i>
        </div>
    </div>
</template>

<style lang="less" scoped>
.photo-preview {
    display: flex;
    flex-direction: row;
    user-select: none;
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
    .arrow-unactive {
        opacity: 0.4;
        pointer-events: none;
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
                default: 3
            },
            width: {
                default: 200,
                validator: function(value) {
                    return value > 0
                }
            },
            height: {
                default: 400,
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
                default: function () {
                    return []
                }
            },
            value: {
                default: 1,
                validator: function(value) {
                    return value >= 0
                }
            }
        },
        data() {
            return {
                idStyle: {
                    height: this.height + 'px'
                },
                showUrls: [],
                photoSliderWidth: this.showNumber * this.width + this.photosGap * (this.showNumber - 1),
                photoStyle: {
                    width: this.width + 'px',
                    height: this.height + 'px',
                    marginRight: this.photosGap + 'px'
                },
                photoSliderLocation: 0,
                index: this.value
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
            },
            leftUnActive: {
                get () {
                    return this.index + (this.showNumber - 2) >= this.photoList.length - 1
                }
            },
            rightUnActive: {
                get () {
                    return this.index <= 1
                }
            }
        },
        methods: {
            setShowUrls() {
                // 根据leftIndex&rightIndex，到 photoList 截取 showUrls
                let leftIndex = this.index - 2
                let rightIndex = leftIndex + this.showNumber + 1
                if (leftIndex <= 0) {
                    this.showUrls = this.photoList.slice(0, rightIndex + 1)
                } else if (rightIndex >= this.photoList.length - 1) {
                    this.showUrls = this.photoList.slice(leftIndex)
                } else {
                    this.showUrls = this.photoList.slice(leftIndex, rightIndex + 1)
                }
            },
            setSliderLocation() {
                if (this.index == 0) {
                    this.photoSliderLocation = this.photosGap + this.width
                } else if (this.index == 1) {
                    this.photoSliderLocation = 0
                } else {
                    this.photoSliderLocation = -(this.photosGap + this.width)
                }
            },
            changeShowUrls(isLeft) {
                if (isLeft) {
                    this.index = this.index + 1
                } else {
                    this.index = this.index - 1
                }
                this.setShowUrls()
                this.setSliderLocation()
                this.$emit('on-change', this.index)
            }
        },    
        created() {
            if (this.photoList.length <= 0) {
                this.photoSliderWidth = 0
                return
            }
            // 验证 index 的传入值
            if (this.index < 1) {
                this.index = 1
            } else if (this.index + this.showNumber - 2 > this.photoList.length - 1) {
                this.index = this.photoList.length - 1 - (this.showNumber - 2)
            }
            // 初始化卡片展示数组 showUrls
            this.setShowUrls()
            this.setSliderLocation()
        }
    }
</script>