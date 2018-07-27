<style scoped lang="scss">
    .button {
        width: 20px;
        height: 20px;
        display: inline-block;
        border: 1px solid black;
        border-radius: 50%;
        background-color: gray;
        cursor: pointer;
    }
</style>

<template>
    <div style="display: flex; flex-direction: row; justify-content: center;height: 35px;">
        <a class="button" href="#" :style="{['background-color'] : cursorColor}" @click.prevent="chooseColor"></a>
        <color-picker
                v-if="enableCursorColorColorPicker"
                :color.sync="cursorColor"
                :x="xCursorColorPicker"
                :y="yCursorColorPicker"
                @close="enableCursorColorColorPicker = false"
        ></color-picker>
    </div>
</template>

<script lang="ts">
    import Vue from 'vue';
    import ColorPicker from './toolbar-components/ColorPicker.vue';

    export default Vue.extend({
        components: {
            ColorPicker,
        },
        data() {
            return {
                cursorColor: '#000000',
                enableCursorColorColorPicker: false,
                xCursorColorPicker: 0,
                yCursorColorPicker: 0,
            };
        },
        methods: {
            chooseColor(evt: Event) {
                const rect = evt.srcElement!.getBoundingClientRect();
                this.xCursorColorPicker = rect.left;
                this.yCursorColorPicker = rect.top;
                this.enableCursorColorColorPicker = true;
            },
        },
        watch: {
            cursorColor: {
                handler(val) {
                    this.$emit('cursor-options', {
                        color: val,
                    });
                },
                immediate: true,
            },
        },
    });
</script>
