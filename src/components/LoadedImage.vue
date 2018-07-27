<template>
    <div style="width: 100%; height: 100%; display: flex; flex-direction: column; justify-content: start;">
        <toolbar @cursor-options="cursorOptionsSet()"></toolbar>
        <div ref="parent" style="width: 100%; height: calc(100% - 35px);display: flex;justify-content: center;align-items: start;">
            <canvas ref="canvas" v-on:mousemove="mouseMove" v-on:mouseover="mouseOver" v-on:mouseup="mouseUp" v-on:mousedown="mouseDown"></canvas>
        </div>
    </div>
</template>

<script lang="ts">
    import Vue from 'vue';
    import * as _ from 'lodash';
    import Toolbar from './loaded-image-components/Toolbar.vue';

    export default Vue.extend({
        components: {
            Toolbar,
        },
        props: {
            imageDataUrl: {
                type: String,
                required: true,
            },
        },
        mounted() {
            this.printImageOnCanvas(this.imageDataUrl);
        },
        data() {
            return {
                cursorOptions: {
                    color: null,
                },
                context: {} as CanvasRenderingContext2D|null,
                clicks: [] as Array<{x: number, y: number, drag: boolean}>,
            };
        },
        methods: {
            computeLenghts(canvas: ClientRect, img: HTMLImageElement)
                : {canvas: { width: number; height: number; }, img: { width: number; height: number; } } {
                let data = {canvas: {width: 0, height: 0}, img: {width: 0, height: 0}};
                let ratioCanvasImg = null;
                if (img.width >= canvas.width && img.height >= canvas.height) {
                    if (img.width >= img.height) {
                        ratioCanvasImg = canvas.width / img.width;
                    } else {
                        ratioCanvasImg = canvas.height / img.height;
                    }
                }

                if (img.width >= canvas.width && img.height < canvas.height) {
                    ratioCanvasImg = canvas.height / img.height;
                }

                if (img.width < canvas.width && img.height >= canvas.height) {
                    ratioCanvasImg = canvas.width / img.width;
                }

                if (img.width < canvas.width && img.height < canvas.height) {
                    ratioCanvasImg = 1;
                }

                data = {
                    img: {
                        width: img.width,
                        height: img.height,
                    },
                    canvas: {
                        width: img.width * ratioCanvasImg!,
                        height: img.height * ratioCanvasImg!,
                    },
                };

                return data;
            },
            cursorOptionsSet(data: any) {
                const options = _.cloneDeep(this.cursorOptions);
                _.assign(options, data);
                this.cursorOptions = options;
            },
            printImageOnCanvas(dataUrlImage: string) {
                const canvas = this.$refs.canvas as HTMLCanvasElement;
                const ctx = canvas.getContext('2d');
                const img = new Image();
                const parentRect = (this.$refs.parent as HTMLElement).getBoundingClientRect();
                img.onload = () => {
                    const calculateLenghts = this.computeLenghts(parentRect, img);
                    canvas.width = calculateLenghts.canvas.width;
                    canvas.height = calculateLenghts.canvas.height;
                    ctx!.drawImage(img, 0, 0, calculateLenghts.img.width, calculateLenghts.img.height,
                                        0, 0, calculateLenghts.canvas.width, calculateLenghts.canvas.height);
                    ctx!.save();
                    this.context = ctx;
                };
                img.src = dataUrlImage;
            },
            mouseOver(evt: Event) {
                // console.log('over', evt);
            },
            mouseUp(evt: Event) {
                // console.log('up', evt);
            },
            mouseDown(evt: Event) {
                // console.log('down', evt);
            },
            mouseMove(evt: MouseEvent) {
                // console.log('move', evt);
                const canvas = this.$refs.canvas as HTMLCanvasElement;
                const x = evt.pageX - canvas.offsetLeft;
                const y = evt.pageY - canvas.offsetTop;
                // console.log(x,y)
            },
        },
        watch: {
            imageDataUrl(val) {
                this.printImageOnCanvas(val);
            },
        },
    });
</script>
