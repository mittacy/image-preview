<template>
    <div class="photo-preview" :style="{height: height + 'px'}">
        <div
            class="arrow"
            :class="{'arrow-unactive': isLeftEnd, 'arrow-active': !isLeftEnd}"
            @click="clickButton(true)">
            <i class="iconfont icon-left"></i>
        </div>
        <div
            class="photo-slider-wrap"
            :style="{height: height + 'px', width: this.showNumber * this.width + this.photosGap * (this.showNumber - 1) + 'px'}">
            <div
                class="photo-slider-hidden"
                :style="{height: height + 'px', width: this.showNumber * this.width + this.photosGap * (this.showNumber - 1) + 'px'}">
                <div
                    class="photo-slider"
                    :style="{
                        right: leftSliderLocation + 'px',
                        opacity: leftSlider.opacity,
                        transition: 'right .3s ease-out 0s'
                    }">
                    <div
                        class="photo-wrap"
                        :style="[leftPhotoStyle, {width: width + 'px'}]"
                        v-for="(photoUrl, index) in leftSlider.showUrls"
                        :key="index">
                        <img
                            class="photo"
                            :style="{width: width + 'px', height: height + 'px'}"
                            :src="photoUrl"/>
                    </div>
                </div>
                <div
                    class="photo-slider"
                    :style="{
                        left: rightSliderLocation + 'px',
                        opacity: rightSlider.opacity,
                        transition: 'left .3s ease-out 0s'
                    }">
                    <div
                        class="photo-wrap"
                        :style="[rightPhotoStyle, {width: width + 'px'}]"
                        v-for="(photoUrl, index) in rightSlider.showUrls"
                        :key="index">
                        <img
                            class="photo"
                            :style="{width: width + 'px', height: height + 'px'}"
                            :src="photoUrl"/>
                    </div>
                </div>
            </div>
        </div>
        <div
            class="arrow"
            :class="{'arrow-unactive': isRightEnd, 'arrow-active': !isRightEnd}"
            @click="clickButton(false)">
            <i class="iconfont icon-right"></i>
        </div>
    </div>
</template>

<style lang="less" scoped>
.photo-preview {
    width: 100%;
    display: flex;
    flex-direction: row;
    align-items: center;
    justify-content: space-between;
    user-select: none;
    .arrow {
        display: flex;
        flex-direction: column;
        justify-content: center;
        align-items: center;
        width: 40px;
        height: 100%;
        color: #333;
        transition: background-color .3s;
        font-size: 30px;
        cursor: pointer;
    }
    .arrow-active:hover {
        color: rgb(81, 90, 110);
    }
    .arrow-unactive {
        color: rgb(81, 90, 110);
        cursor: no-drop;
    }
    .iconfont {
        font-size: 20px;
    }
    .photo-slider-wrap {
        margin: 0 auto;
        .photo-slider-hidden {
            position: relative;
            overflow: hidden;
            .photo-slider {
                position: absolute;
                display: flex;
                flex-direction: row;
                justify-content: center;
                align-items: center;
                .photo-wrap {
                    display: flex;
                    flex-direction: row;
                    justify-content: center;
                    .photo {
                        border-width: 1px;
                        border-color: rgba(0,0,0,.4);
                        border-style: solid;
                    }
                }
            }
        }
    }
}
</style>

<script>
    export default {
        name: 'ImagePreview',
        props: {
            showNumber: {
                type: Number,
                default: 3
            },
            height: {
                type: Number,
                default: 400,
                validator: function(value) {
                    return value > 0;
                }
            },
            // 宽比高
            ratio: {
                type: Number,
                default: 0.5
            },
            photosGap: {
                type: Number,
                default: 10,
                validator: function(value) {
                    return value > 0;
                }
            },
            photoList: {
                type: Array,
                default: function () {
                    return [];
                }
            },
            value: {
                type: Number,
                default: 1,
                validator: function(value) {
                    return value >= 0;
                }
            }
        },
        data() {
            return {
                // 已经缓存的 photo 在 photoList 中的索引记录
                photoListRecord: {
                    initIndex: 0,
                    leftIndex: 0,
                    rightIndex: 0
                },
                // 当前的value
                oldValue: 1,
                // 当前操控的滑块
                currentSilder: this.rightSlider,
                // 左滑块
                leftSlider: {
                    active: true,
                    showUrls: [],
                    opacity: 0,
                },
                // 右滑块
                rightSlider: {
                    active: true,
                    showUrls: [],
                    opacity: 1
                },
                // photoSliderWrapWidth: ,
                leftPhotoStyle: {
                    height: this.height + 'px',
                    marginLeft: this.photosGap + 'px'
                },
                rightPhotoStyle: {
                    height: this.height + 'px',
                    marginRight: this.photosGap + 'px'
                }
            }
        },
        watch: {
            _value() {
                // 外部组件修改value
                if (this.value != this.oldValue) {
                    this.init();
                }
            }
        },
        computed: {
            // 每次滑动的距离
            slidingDistance: {
                get () {
                    return this.photosGap + this.width;
                }
            },
            // css 样式
            previewWidth: {
                get () {
                    return this.width * this.showNumber + this.photosGap * (this.showNumber + 1) + 80;
                }
            },
            width: {
                get () {
                    return this.height * this.ratio
                }
            },
            _value: {
                get () {
                    return this.value;
                },
                set (value) {
                    this.$emit('input', value);
                    if (value - this.oldValue === 1) {
                        this.setSlider(false);
                    }
                    else if (value - this.oldValue === -1) {
                        this.setSlider(true);
                    }
                    // 4. 更新 value
                    this.oldValue = value;
                }
            },
            isLeftEnd: {
                get() {
                    return this.value <= 1;
                }
            },
            isRightEnd: {
                get() {
                    return this.value + this.showNumber > this.photoList.length;
                }
            },
            leftSliderLocation: {
                get() {
                    return (this.value - this.photoListRecord.initIndex) * this.slidingDistance;
                }
            },
            rightSliderLocation: {
                get() {
                    return - (this.value - this.photoListRecord.initIndex) * this.slidingDistance;
                }
            }
        },
        methods: {
            verifyProps() {
                // 校验纠正 value，由于 value 的卡片始终位于第二个可见位，所以 1 <= value <= this.photoList.length - this.showNumber + 1
                if (this.value < 1) {
                    this._value = 1;
                }
                else if (this.value > this.photoList.length - this.showNumber + 1) {
                    this._value = this.photoList.length - this.showNumber + 1;
                }
            },
            // 初始化 缓存左右索引记录 和 左右滑片
            init() {
                this.verifyProps();
                this.$nextTick(() => {
                    this.initPhotoListRecord();
                    this.initSliders();
                });
            },
            initPhotoListRecord() {
                this.oldValue = this.value;
                this.photoListRecord.initIndex = this.value;
                // 左尽头
                if (this.isLeftEnd) {
                    this.setLeftRightIndex(0, this.showNumber);
                }
                // 右尽头
                else if (this.isRightEnd) {
                    this.setLeftRightIndex(this.value - 2, this.photoList.length - 1);
                }
                else {
                    this.setLeftRightIndex(this.value - 2, this.value + this.showNumber - 1);
                }
            },
            // number = 0 | 1 | 2
            // 0 关闭左滑块
            // 1 关闭右滑块
            // 2 都开启
            closeSlider(number) {
                if (number === 0) {
                    console.log("关闭左滑块");
                    this.leftSlider.active = false;
                    this.leftSlider.opacity = 0;
                    this.rightSlider.active = true;
                    this.rightSlider.opacity = 1;
                    this.currentSilder = this.rightSlider;
                }
                else if (number === 1) {
                    console.log("关闭右滑块");
                    this.leftSlider.active = true;
                    this.leftSlider.opacity = 1;
                    this.rightSlider.active = false;
                    this.rightSlider.opacity = 0;
                    this.currentSilder = this.leftSlider;
                }
                else {
                    this.leftSlider.active = true;
                    this.leftSlider.opacity = 0;
                    this.rightSlider.active = true;
                    this.rightSlider.opacity = 1;
                    this.currentSilder = this.rightSlider;
                }
            },
            initSliders() {
                // 只有右滑块
                if (this.isLeftEnd) {
                    this.closeSlider(0);
                    let arr = this.photoList.slice(this.photoListRecord.leftIndex, this.photoListRecord.rightIndex + 1);
                    this.rightSlider.showUrls =  arr;
                }
                // 只有左滑块
                else if (this.isRightEnd) {
                    this.closeSlider(1);
                    let arr = this.photoList.slice(this.photoListRecord.leftIndex, this.photoListRecord.rightIndex + 1);
                    this.leftSlider.showUrls = arr;
                }
                // 两个滑块
                else {
                    this.closeSlider(2);
                    let leftArr = this.photoList.slice(this.photoListRecord.leftIndex, this.photoListRecord.rightIndex);
                    let rightArr = this.photoList.slice(this.photoListRecord.leftIndex + 1, this.photoListRecord.rightIndex + 1);
                    this.leftSlider.showUrls = leftArr;
                    this.rightSlider.showUrls = rightArr;
                }
            },
            clickButton(isLeftButton) {
                // 判断 右尽头 点击右滑动
                if (this.isRightEnd && !isLeftButton) {
                    return;
                }
                // 判断 左尽头 点击左滑动
                if (this.isLeftEnd && isLeftButton) {
                    return;
                }
                if (isLeftButton) {
                    this._value = this.value - 1;
                } else {
                    this._value = this.value + 1;
                }
                // this.setSlider(isLeftButton);
            },
            // 设置滑片的显示隐藏 以及 新增卡片
            setSlider(isLeftButton) {
                // 1. 切换滑片(如果需要)
                if (this.leftSlider.active && this.rightSlider.active && this.value === this.photoListRecord.initIndex) {
                    if ((isLeftButton && this.currentSilder != this.leftSlider)
                        || (!isLeftButton && this.currentSilder != this.rightSlider)) {
                        this.swapSliderShow();
                    }
                }
                // 2. 新增卡片(如果需要)
                this.$nextTick(() => {
                    this.addPhoto(isLeftButton);
                    this.$emit('before-slide-change', this.value);
                })
            },
            setLeftRightIndex(left, right) {
                this.photoListRecord.leftIndex = left;
                this.photoListRecord.rightIndex = right;
            },
            swapSliderShow() {
                this.currentSilder.opacity = 0;
                this.currentSilder = this.currentSilder === this.rightSlider ? this.leftSlider : this.rightSlider;
                this.currentSilder.opacity = 1;
            },
            addPhoto(isLeftButton) {
                let newIndex = 0;
                if (!isLeftButton) {
                    newIndex = this.value + this.showNumber - 1;
                } else {
                    newIndex = this.value - 2;
                }
                if (!this.isNeedAddPhoto(newIndex)) {
                    return;
                }
                // 点击左箭头，向右滑动，在左边添加卡片
                if (isLeftButton) {
                    this.currentSilder.showUrls = [this.photoList[newIndex]].concat(this.currentSilder.showUrls);
                    this.photoListRecord.leftIndex = newIndex;
                    return;
                }
                // 点击右箭头，向左滑动，在右边添加卡片
                this.currentSilder.showUrls = this.currentSilder.showUrls.concat(this.photoList[newIndex]);
                this.photoListRecord.rightIndex = newIndex;
            },
            isNeedAddPhoto(newIndex) {
                return newIndex <= this.photoList.length - 1
                    && newIndex >= 0
                    && (this.photoListRecord.leftIndex != this.photoListRecord.rightIndex && (newIndex < this.photoListRecord.leftIndex || newIndex > this.photoListRecord.rightIndex));
            },
            // photoHeightWidth() {
            //     let img_url = 'http://object.appspeed.baidu-int.com/backlog-113/2bc5fa1a8a8c1b3e4f1d06b6587e028b/16bba03437d794ab1e2dc6cfaf6fde7b/image-053.jpg';
            //     let img = new Image();
            //     img.src = img_url;
            //     if (img.complete) {
            //         console.log('width: ', img.width, 'height: ', img.height);
            //     } else {
            //         img.onload = function() {
            //             console.log('width: ', img.width, 'height: ', img.height);
            //         }
            //     }
            // }
        },
        created() {
            // 1. 设置宽度
            if (this.photoList.length <= 0) {
                this.photoSliderWrapWidth = 0;
                return;
            }
            // 2. 验证纠正 props 的值
            this.init();
            console.log("this.width:", this.width);
            console.log("slidingDistance:", this.slidingDistance);
        }
    }
</script>