<template>
    <div style="width: 100%; height: 100%; display: flex; flex-direction: column; justify-content: start;">
        <toolbar @cursor-options="cursorOptionsSet" @restore="restore" :theme="theme"></toolbar>
        <div ref="parent" style="width: 100%; height: calc(100% - 35px);display: flex;justify-content: center;align-items: start;">
            <canvas
                    ref="canvas"
                    v-on:mousemove="mouseMove"
                    v-on:mouseover="mouseOver"
                    v-on:mouseup="mouseUp"
                    v-on:mousedown="mouseDown"
                    v-on:mouseleave="mouseLeave"
            ></canvas>
        </div>
    </div>
</template>

<script lang="ts">
    import Vue from 'vue';
    import * as _ from 'lodash';
    import Toolbar from './loaded-image-components/Toolbar.vue';

    interface LocalPoint {
        x: number;
        y: number;
        drag: boolean;
    }

    export default Vue.extend({
        components: {
            Toolbar,
        },
        props: {
            imageDataUrl: {
                type: String,
                required: true,
            },
            theme: {
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
                    enabled: false,
                    color: '',
                },
                context: {} as CanvasRenderingContext2D|null,
                clicks: [] as LocalPoint[],
                paint: false,
            };
        },
        methods: {
            addClick(data: LocalPoint) {
                this.clicks.push(data);
            },
            computeLenghts(canvas: ClientRect, img: HTMLImageElement)
                : {canvas: { width: number; height: number; }, img: { width: number; height: number; } } {
                let data = {canvas: {width: 0, height: 0}, img: {width: 0, height: 0}};
                let ratioCanvasImg = null;
                if (img.width >= canvas.width && img.height >= canvas.height) {
                    if (img.width >= img.height) {
                        // console.log('1.1');
                        ratioCanvasImg = canvas.width / img.width;
                    } else {
                        // console.log('1.2');
                        ratioCanvasImg = canvas.height / img.height;
                    }
                }

                if (img.width >= canvas.width && img.height < canvas.height) {
                    if (img.width >= img.height) {
                        // console.log('2.1');
                        ratioCanvasImg = canvas.width / img.width;
                    } else {
                        // console.log('2.2');
                        ratioCanvasImg = canvas.height / img.height;
                    }
                }

                if (img.width < canvas.width && img.height >= canvas.height) {
                    if (img.width >= img.height) {
                        // console.log('3.1');
                        ratioCanvasImg = canvas.height / img.height;
                    } else {
                        // console.log('3.2');
                        ratioCanvasImg = canvas.width / img.width;
                    }
                }

                if (img.width < canvas.width && img.height < canvas.height) {
                    ratioCanvasImg = 1;
                    // console.log('4');
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
                this.clicks = [];
            },
            estrapolateCoords(evt: MouseEvent): { x: number, y: number} {
                const canvas = this.$refs.canvas as HTMLCanvasElement;
                const x = evt.pageX - canvas.getBoundingClientRect().left;
                const y = evt.pageY - canvas.getBoundingClientRect().top;

                return {
                    x,
                    y,
                };
            },
            export() {
                this.$emit('export', (this.$refs.canvas as HTMLCanvasElement).toDataURL() );
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
                    // ctx!.save();
                    this.context = ctx;
                };
                img.src = dataUrlImage;
            },
            mouseOver(evt: MouseEvent) {
                // console.log('over', evt);
            },
            mouseUp(evt: Event) {
                // console.log('up', evt);
                this.paint = false;
                this.export();
            },
            mouseDown(evt: MouseEvent) {
                // console.log('down', evt);
                if (!this.cursorOptions.enabled || evt.button !== 0) {
                    return;
                }

                const coords = this.estrapolateCoords(evt);

                this.paint = true;
                this.addClick({ x: coords.x, y: coords.y, drag: false});
                this.redraw();
            },
            mouseLeave() {
                this.paint = false;
                this.export();
            },
            mouseMove(evt: MouseEvent) {
                // console.log('move', evt);
                if (this.paint) {
                    const coords = this.estrapolateCoords(evt);
                    this.addClick({ x: coords.x, y: coords.y, drag: true});
                    this.redraw();
                }
                // console.log(x,y)
            },
            redraw() {
                // this.context.clearRect(0, 0, context.canvas.width, context.canvas.height); // Clears the canvas

                this.context!.strokeStyle = this.cursorOptions.color;
                this.context!.lineJoin = 'round';
                this.context!.lineWidth = 5;

                for (let i = 0; i < this.clicks.length; i++) {
                    this.context!.beginPath();
                    if (this.clicks[i].drag && i) {
                        this.context!.moveTo(this.clicks[i - 1].x, this.clicks[i - 1].y);
                    } else {
                        this.context!.moveTo(this.clicks[i].x - 1, this.clicks[i].y);
                    }
                    this.context!.lineTo(this.clicks[i].x, this.clicks[i].y);
                    this.context!.closePath();
                    this.context!.stroke();
                }
            },
            restore() {
                this.context!.clearRect(0, 0, this.context!.canvas.width, this.context!.canvas.height);
                this.printImageOnCanvas(this.imageDataUrl);
                this.clicks = [];
                this.export();
            },
        },
        watch: {
            imageDataUrl(val) {
                this.printImageOnCanvas(val);
            },
        },
    });
</script>
