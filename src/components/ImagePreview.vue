<template>
    <div class="photo-preview" :style="{height: height + 20 + 'px', width: previewWidth + 'px'}">
        <div
            class="arrow"
            :class="{'arrow-unactive': isLeftEnd, 'arrow-active': !isLeftEnd}"
            @click="moveSlider(true)">
            <i class="iconfont icon-left"></i>
        </div>
        <div
            class="photo-slider-wrap"
            :style="{height: height + 'px', width: photoSliderWrapWidth + 'px'}">
            <div
                class="photo-slider-hidden"
                :style="{height: height + 'px', width: photoSliderWrapWidth + 'px'}">
                <div
                    class="photo-slider"
                    :style="{
                        right: leftSlider.location + 'px',
                        opacity: leftSlider.opacity,
                        transition: 'right .3s ease-out 0s'
                    }">
                    <img
                        class="photo"
                        :style="leftPhotoStyle"
                        v-for="(photoUrl, index) in leftSlider.showUrls"
                        :src="photoUrl"
                        :key="index"/>
                </div>
                <div
                    class="photo-slider"
                    :style="{
                        left: rightSlider.location + 'px',
                        opacity: rightSlider.opacity,
                        transition: 'left .3s ease-out 0s'
                    }">
                    <img
                        class="photo"
                        :style="rightPhotoStyle"
                        v-for="(photoUrl, index) in rightSlider.showUrls"
                        :src="photoUrl"
                        :key="index"/>
                </div>
            </div>
        </div>
        <div
            class="arrow"
            :class="{'arrow-unactive': isRightEnd, 'arrow-active': !isRightEnd}"
            @click="moveSlider(false)">
            <i class="iconfont icon-right"></i>
        </div>
    </div>
</template>

<style lang="less" scoped>
.photo-preview {
    display: flex;
    flex-direction: row;
    align-items: center;
    user-select: none;
    border-top: 2px solid #1559a0;
    border-bottom: 2px solid #1559a0;
    .arrow {
        display: flex;
        flex-direction: column;
        justify-content: center;
        align-items: center;
        width: 40px;
        height: 100%;
        color: #fff; 
        transition: background-color .3s;
        font-size: 30px;
        cursor: pointer;
    }
    .arrow-active {
        background-color: #1559a0;
    }
    .arrow-active:hover {
        background-color: #0d6dd2;
    }
    .arrow-unactive {
        color: #555;
        background-color: #e9eaec;
        // pointer-events: none;
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
            width: {
                type: Number,
                default: 200,
                validator: function(value) {
                    return value > 0;
                }
            },
            height: {
                type: Number,
                default: 400,
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
            }
        },
        data() {
            return {
                // 每次滑动的距离
                slidingDistance: this.photosGap + this.width,
                // 已经缓存的 photo 在 photoList 中的索引记录
                photoListRecord: {
                    initIndex: 0,
                    leftIndex: 0,
                    rightIndex: 0
                },
                currentSilder: this.rightSlider,
                // 左滑块
                leftSlider: {
                    active: true,
                    showUrls: [],
                    location: 0,
                    opacity: 0,
                },
                // 右滑块
                rightSlider: {
                    active: true,
                    showUrls: [],
                    location: 0,
                    opacity: 1
                },
                // css 样式
                previewWidth: this.width * this.showNumber + this.photosGap * (this.showNumber + 1) + 80,
                photoSliderWrapWidth: this.showNumber * this.width + this.photosGap * (this.showNumber - 1),
                leftPhotoStyle: {
                    width: this.width + 'px',
                    height: this.height + 'px',
                    marginLeft: this.photosGap + 'px'
                },
                rightPhotoStyle: {
                    width: this.width + 'px',
                    height: this.height + 'px',
                    marginRight: this.photosGap + 'px'
                }
            }
        },
        computed: {
            _value: {
                get () {
                    return this.value;
                },
                set (value) {
                    this.$emit('input', value);
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
            initPhotoListRecord(index) {
                this.photoListRecord.initIndex = index;
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
            initSliders() {
                // 只有右滑块
                if (this.isLeftEnd) {
                    this.leftSlider.active = false;
                    this.currentSilder = this.rightSlider;
                    let arr = this.photoList.slice(this.photoListRecord.leftIndex, this.photoListRecord.rightIndex + 1);
                    this.initRightSlider(arr, 0);
                }
                // 只有左滑块
                else if (this.isRightEnd) {
                    this.rightSlider.active = false;
                    this.leftSlider.opacity = 1;
                    this.rightSlider.opacity = 0;
                    this.currentSilder = this.leftSlider;
                    let arr = this.photoList.slice(this.photoListRecord.leftIndex, this.photoListRecord.rightIndex + 1);
                    this.initLeftSlider(arr, 0);
                }
                // 两个滑块
                else {
                    this.currentSilder = this.rightSlider;
                    let leftArr = this.photoList.slice(this.photoListRecord.leftIndex, this.photoListRecord.rightIndex);
                    let rightArr = this.photoList.slice(this.photoListRecord.leftIndex + 1, this.photoListRecord.rightIndex + 1);
                    this.initLeftSlider(leftArr, 0);
                    this.initRightSlider(rightArr, 0);
                }
            },
            moveSlider(isLeftButton) {
                // 判断 右尽头 点击右滑动
                if (this.isRightEnd && !isLeftButton) {
                    return;
                }
                // 判断 左尽头 点击左滑动
                if (this.isLeftEnd && isLeftButton) {
                    return;
                }
                // 1. 切换滑片(如果需要)
                if (this.leftSlider.active && this.rightSlider.active) {
                    if ((isLeftButton && this.currentSilder != this.leftSlider)
                        || (!isLeftButton && this.currentSilder != this.rightSlider)) {
                        this.swapSliderShow();
                    }
                }
                // 2. 滑动
                this.slidingSlider(isLeftButton);
                // 3. 更新 value
                if (isLeftButton) {
                    this._value = this.value - 1;
                }
                else {
                    this._value = this.value + 1;
                }
                this.$nextTick(() => {
                    // 4. 新增卡片(如果需要)
                    this.addPhoto(isLeftButton);
                    this.$emit('on-change', this.value);
                });
            },
            setLeftRightIndex(left, right) {
                this.photoListRecord.leftIndex = left;
                this.photoListRecord.rightIndex = right;
            },
            initLeftSlider(arr, location) {
                this.leftSlider.showUrls = arr;
                this.leftSlider.location = location;
            },
            initRightSlider(arr, location) {
                this.rightSlider.showUrls = arr;
                this.rightSlider.location = location;
            },
            swapSliderShow() {
                if (this.value === this.photoListRecord.initIndex) {
                    this.currentSilder.opacity = 0;
                    this.currentSilder = this.currentSilder === this.rightSlider ? this.leftSlider : this.rightSlider;
                    this.currentSilder.opacity = 1;
                }
            },
            slidingSlider(isLeftButton) {
                if (isLeftButton) {
                    if (this.currentSilder === this.leftSlider) {
                        this.currentSilder.location -= this.slidingDistance;
                        return;
                    }
                    this.currentSilder.location += this.slidingDistance;
                }
                else {
                    if (this.currentSilder === this.leftSlider) {
                        this.currentSilder.location += this.slidingDistance;
                        return;
                    }
                    this.currentSilder.location -= this.slidingDistance;
                }
            },
            isNeedAddPhoto(newIndex) {
                return newIndex <= this.photoList.length - 1
                    && newIndex >= 0
                    && (newIndex < this.photoListRecord.leftIndex || newIndex > this.photoListRecord.rightIndex);
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
            }
        },
        created() {
            // 1. 设置宽度
            if (this.photoList.length <= 0) {
                this.photoSliderWrapWidth = 0;
                this.previewWidth = 0;
                return;
            }
            // 2. 验证纠正 props 的值
            this.verifyProps();
            this.$nextTick(() => {
                // 3. 初始化 缓存左右索引记录
                this.initPhotoListRecord(this.value);
                // 4. 初始化 左右滑片
                this.initSliders();
            });
        }
    }
</script>