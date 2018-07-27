<template>
    <div style="width: 100%; height: 100%;">
        <app :image-data-url="imageDataUrl"></app>
        <input ref="input" type="file" @change="file()"><br>
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
        },
    });
</script>
