<template>
    <div style="width: 100%; height: 100%;">
        <app :image-data-url="imageDataUrl" @new-image="logNewImage"></app>
        <input ref="input" type="file" @change="file()"><br>
        <button @click="download">Download Image</button>
    </div>
</template>

<script lang="ts">
    import Vue from 'vue';
    import App from './App.vue';

    export default Vue.extend({
        components: {
            App,
        },
        data() {
            return {
                imageDataUrl: null,
                newImage: '',
            };
        },
        methods: {
            file() {
                const file    = (this.$refs.input as HTMLInputElement).files![0];
                const reader  = new FileReader();

                reader.addEventListener('load', () => {
                    this.imageDataUrl = reader.result;
                }, false);

                if (file) {
                    reader.readAsDataURL(file);
                }
            },
            logNewImage(data: any) {
                this.newImage = data.image;
            },
            download() {
                if (this.newImage) {
                    this.downloadURI(this.newImage, 'new-image.png');
                }
            },
            downloadURI(uri: string, name: string) {
                const link = document.createElement('a');
                link.download = name;
                link.href = uri;
                document.body.appendChild(link);
                link.click();
                document.body.removeChild(link);
            },
        },
    });
</script>
