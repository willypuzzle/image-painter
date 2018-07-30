<template>
    <div style="width: 100%; height: 100%;">
        <unloaded-image v-if="!imageDataUrl"></unloaded-image>
        <loaded-image v-else :image-data-url="imageDataUrl" @export="$emit('new-image', {image: $event})"></loaded-image>
    </div>
</template>

<script lang="ts">
import Vue from 'vue';
import LoadedImage from './components/LoadedImage.vue';
import UnloadedImage from './components/UnloadedImage.vue';

export default Vue.extend({
    components: {
        LoadedImage,
        UnloadedImage,
    },
    props: {
        imageDataUrl: {
            type: String,
        },
    },
});
</script>

<style>

</style>
