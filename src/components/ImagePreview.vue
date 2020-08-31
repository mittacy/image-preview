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
            width: {
                type: Number,
                default: 200,
                validator: function(value) {
                    return value > 0;
                }
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
            },
            sliderNum: {
                type: Number,
                default: 1,
                validator: function(value) {
                    return value >= 1;
                }
            }
        },
        data() {
            return {
                photoListRecord: {
                    initIndex: 0,
                    leftIndex: 0,
                    rightIndex: 0
                },
                oldValue: 1,
                currentSilder: this.rightSlider,
                leftSlider: {
                    active: true,
                    showUrls: [],
                    opacity: 0,
                },
                rightSlider: {
                    active: true,
                    showUrls: [],
                    opacity: 1
                },
                cacheNum: this.sliderNum * 2,
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
        computed: {
            slidingDistance() {
                return this.photosGap + this.width;
            },
            previewWidth() {
                return this.width * this.showNumber + this.photosGap * (this.showNumber + 1) + 80;
            },
            _value: {
                get () {
                    return this.value;
                },
                set (value) {
                    this.$emit('input', value);
                    if (value - this.oldValue > 0) {
                        this.setSlider(false);
                    }
                    else if (value - this.oldValue < 0) {
                        this.setSlider(true);
                    }
                    this.oldValue = value;
                }
            },
            isLeftEnd() {
                return this.value - 1 <= 0;
            },
            isRightEnd() {
                return this.value + this.showNumber > this.photoList.length;
            },
            leftSliderLocation() {
                return (this.value - this.photoListRecord.initIndex) * this.slidingDistance;
            },
            rightSliderLocation() {
                return - (this.value - this.photoListRecord.initIndex) * this.slidingDistance;
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
            init(val) {
                this.verifyProps();
                if (!(typeof val === "undefined")) {
                    this._value = val;
                }
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
                    this.setLeftRightIndex(0, this.showNumber + this.cacheNum - 1);
                }
                // 右尽头
                else if (this.isRightEnd) {
                    this.setLeftRightIndex(this.value - this.cacheNum - 1, this.photoList.length - 1);
                }
                else {
                    this.setLeftRightIndex(this.value - this.cacheNum - 1, this.value + this.showNumber + this.cacheNum - 2);
                }
            },
            // number = 0 | 1 | 2
            // 0 关闭左滑块
            // 1 关闭右滑块
            // 2 都开启
            closeSlider(number) {
                if (number === 0) {
                    this.leftSlider.active = false;
                    this.leftSlider.opacity = 0;
                    this.rightSlider.active = true;
                    this.rightSlider.opacity = 1;
                    this.currentSilder = this.rightSlider;
                }
                else if (number === 1) {
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
                    let leftArr = this.photoList.slice(this.photoListRecord.leftIndex, this.photoListRecord.initIndex + this.showNumber - 1);
                    let rightArr = this.photoList.slice(this.photoListRecord.initIndex - 1, this.photoListRecord.rightIndex + 1);
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
                    // 左尽头如果不够滑片，控制移动到尽头
                    if (this.value - this.sliderNum < 1) {
                        this._value = 1;
                        return;
                    }
                    // 需要切换滑片时
                    if (this.rightSlider.opacity === 1
                        && this.value > this.photoListRecord.initIndex
                        && this.value < this.photoListRecord.initIndex + this.sliderNum) {
                        this._value = this.photoListRecord.initIndex;
                        return;
                    }
                    this._value = this.value - this.sliderNum;
                    return;
                }
                // 右尽头如果不够滑片，控制移动到尽头
                if (this.value + this.sliderNum > this.photoList.length - this.showNumber + 1) {
                    this._value = this.photoList.length - this.showNumber + 1;
                    return;
                }
                // 需要切换滑片时
                if (this.leftSlider.opacity === 1
                    && this.value < this.photoListRecord.initIndex
                    && this.value > this.photoListRecord.initIndex - this.sliderNum) {
                    this._value = this.photoListRecord.initIndex;
                    return;
                }
                this._value = this.value + this.sliderNum;
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
                if (left < 0) {
                    left = 0;
                }
                this.photoListRecord.leftIndex = left;
                this.photoListRecord.rightIndex = right;
            },
            swapSliderShow() {
                this.currentSilder.opacity = 0;
                this.currentSilder = this.currentSilder === this.rightSlider ? this.leftSlider : this.rightSlider;
                this.currentSilder.opacity = 1;
            },
            addPhoto(isLeftButton) {
                let newLeftIndex, newRightIndex;
                if (isLeftButton) {
                    newRightIndex = this.value - 2 - this.sliderNum;
                    if (!this.isNeedAddPhoto(true, newRightIndex)) {
                        return;
                    }
                    newLeftIndex = newRightIndex - this.sliderNum + 1;
                    if (newLeftIndex < 0) {
                        newLeftIndex = 0;
                    }
                    this.currentSilder.showUrls = this.photoList.slice(newLeftIndex, newRightIndex + 1).concat(this.currentSilder.showUrls);
                    this.photoListRecord.leftIndex = newLeftIndex;
                    return;
                }
                newLeftIndex = this.value + this.showNumber - 1 + this.sliderNum;
                if (!this.isNeedAddPhoto(false, newLeftIndex)) {
                    return;
                }
                newRightIndex = newLeftIndex + this.sliderNum - 1;
                this.currentSilder.showUrls = this.currentSilder.showUrls.concat(this.photoList.slice(newLeftIndex, newRightIndex+1));
                this.photoListRecord.rightIndex = newRightIndex;
            },
            isNeedAddPhoto(isLeftButton, newIndex) {
                if (isLeftButton) {
                    return newIndex <= this.photoList.length - 1
                        && newIndex >= 0
                        && (this.photoListRecord.leftIndex != this.photoListRecord.rightIndex && newIndex < this.photoListRecord.leftIndex);
                }
                return newIndex <= this.photoList.length - 1
                    && newIndex >= 0
                    && (this.photoListRecord.leftIndex != this.photoListRecord.rightIndex && newIndex > this.photoListRecord.rightIndex);
            }
        },
        created() {
            // 1. 设置宽度
            if (this.photoList.length <= 0) {
                this.photoSliderWrapWidth = 0;
                return;
            }
            // 2. 验证纠正 props 的值
            this.init();
        }
    }
</script>