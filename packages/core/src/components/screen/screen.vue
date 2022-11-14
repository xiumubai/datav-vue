<template>
    <div class="bb-screen flex_column" :style="style">
        <screen-title class="flex_none" :text="title" :width="textWidth" :font-size="textFontSize"></screen-title>
        <div class="bb-screen__container flex_1">
            <slot></slot>
        </div>
    </div>
</template>

<script>
import ScreenTitle from "./screen-title.vue";
import { px2vw } from "../../utils/string.util";
export default {
    name: 'ScreenAdapter',
    data() {
        return {
            style: {
                width: px2vw(this.w, this.w),
                height: px2vw(this.h, this.w),
                transform: 'scale(1) translate(-50%, -50%)', // 默认不缩放，垂直水平居中
            },
        };
    },
    components: {
        ScreenTitle
    },
    props: {
        w: { // 设计图尺寸宽
            type: Number,
            default: 1920,
        },
        h: { // 设计图尺寸高
            type: Number,
            default: 1080,
        },
        textWidth: {
            type: Number,
            default: 612,
        },
        textFontSize: {
            type: Number,
            default: 0,
        },
        title: {
            type: String,
            default: ''
        },
    },
    mounted() {
        this.setScale();
        this.onresize = this.debounce(() => this.setScale(), 100);
        window.addEventListener('resize', this.onresize);
    },
    methods: {
        // 防抖
        debounce(fn, t) {
            const delay = t || 500;
            let timer;
            return function () {
                const args = arguments;
                if (timer) {
                    clearTimeout(timer);
                }
                const context = this;
                timer = setTimeout(() => {
                    timer = null;
                    fn.apply(context, args);
                }, delay);
            };
        },
        // 获取缩放比例
        getScale() {
            const w = window.innerWidth / this.w + 0.2;
            const h = window.innerHeight / this.h + 0.2;
            return w < h ? w : h;
        },
        // 设置缩放比例
        setScale() {
            this.style.transform = `scale(${this.getScale()}) translate(-50%, -50%)`;
        },
    },
    beforeDestroy() {
        window.removeEventListener('resize', this.onresize);
    },
};
</script>

<style scoped lang="less">
.bb-screen {
    background: #012746;
    box-sizing: border-box;
    padding: 20px 22px;
    transform-origin: 0 0;
    position: absolute;
    top: 50%;
    left: 50%;
}

.bb-screen__container {
    margin-top: 12px;
}
</style>
