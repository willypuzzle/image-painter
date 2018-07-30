<style scoped lang="scss">
    a {
        margin: 5px;
    }

    .button {
        color: grey;

        &.delete {
            &:hover {
                color: red;
            }
        }
    }

    .enabled {
        color: black;
    }
</style>

<template>
    <div style="display: flex; flex-direction: row; justify-content: start;height: 50px;">
        <a href="#">
            <img v-if="theme === 'default'" style="height: 36px;" src="../../../src/assets/pen.png" @click.prevent="toggleCursor">
            <i v-else-if="theme === 'smilechat'" :class="{ button: true, enabled: this.cursorEnabled, ['idsign-livechat-icon-modifica']: true }"></i>
        </a>
        <a href="#" @click.prevent="chooseColor">
            <img v-if="theme === 'default'" src="../../../src/assets/colors.png">
            <i v-else-if="theme === 'smilechat'" class="idsign-livechat-icon-colore" :style="{['color'] : cursorColor }"></i>
        </a>
        <color-picker
                v-if="enableCursorColorColorPicker"
                :color.sync="cursorColor"
                :x="xCursorColorPicker"
                :y="yCursorColorPicker"
                @close="enableCursorColorColorPicker = false"
        ></color-picker>
        <a href="#">
            <img v-if="theme === 'default'" style="height: 36px;" src="../../../src/assets/restore.png" @click.prevent="$emit('restore')">
            <i class="button delete idsign-livechat-icon-cancella" v-else-if="theme === 'smilechat'"></i>
        </a>
    </div>
</template>

<script lang="ts">
    import Vue from 'vue';
    import ColorPicker from './toolbar-components/ColorPicker.vue';

    export default Vue.extend({
        components: {
            ColorPicker,
        },
        props: {
            theme: {
                type: String,
                required: true,
            },
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
