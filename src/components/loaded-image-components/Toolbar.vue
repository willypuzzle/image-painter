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

    .enabled {
        background-color: gray;
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
        <a href="#"><img style="height: 36px;" src="../../../src/assets/restore.png" @click.prevent="$emit('restore')"></a>
        <a :class="{enabled: this.cursorEnabled}" href="#"><img style="height: 36px;" src="../../../src/assets/pen.png" @click.prevent="toggleCursor"></a>
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
                cursorEnabled: false,
            };
        },
        methods: {
            chooseColor(evt: Event) {
                const rect = evt.srcElement!.getBoundingClientRect();
                this.xCursorColorPicker = rect.left;
                this.yCursorColorPicker = rect.top;
                this.enableCursorColorColorPicker = true;
            },
            toggleCursor() {
                this.cursorEnabled = !this.cursorEnabled;
                this.$emit('cursor-options', {
                    enabled: this.cursorEnabled,
                });
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
