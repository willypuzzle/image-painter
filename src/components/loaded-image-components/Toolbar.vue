<style scoped lang="scss">
    .body-button {
        display: inline-block;
        margin: 5px;
        width: 36px;
        height: 36px;

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

        .icon {
            width: 36px;
            height: 36px;
        }
    }
</style>

<template>
    <div ref="parent" style="display: flex; flex-direction: row; justify-content: start;height: 50px;">
        <a class="body-button" href="#" @click.prevent="toggleCursor">
            <img v-if="theme === 'default'" style="height: 36px;" src="../../../src/assets/pen.png">
            <i v-else-if="theme === 'smilechat'" :class="{ button: true, enabled: this.cursorEnabled, ['idsign-livechat-icon-modifica']: true, icon: true }"></i>
        </a>
        <a class="body-button" href="#" @click.prevent="chooseColor">
            <img v-if="theme === 'default'" src="../../../src/assets/colors.png">
            <i v-else-if="theme === 'smilechat'" class="idsign-livechat-icon-colore icon" :style="{['color'] : cursorColor }"></i>
        </a>
        <color-picker
                v-if="enableCursorColorColorPicker"
                :color.sync="cursorColor"
                :x="xCursorColorPicker"
                :y="yCursorColorPicker"
                @close="enableCursorColorColorPicker = false"
        ></color-picker>
        <a class="body-button" href="#" @click.prevent="$emit('restore')">
            <img v-if="theme === 'default'" style="height: 36px;" src="../../../src/assets/restore.png">
            <i class="button delete idsign-livechat-icon-cancella icon" v-else-if="theme === 'smilechat'"></i>
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
            chooseColor(evt: MouseEvent) {
                const rect = this.$refs.parent!.getBoundingClientRect();
                this.xCursorColorPicker = evt.clientX - rect.left;
                this.yCursorColorPicker = evt.clientY - rect.top;
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
