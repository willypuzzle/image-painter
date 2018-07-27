<template>
    <div :style="style">
        <compact :value="color" @input="updateValue"></compact>
    </div>
</template>

<script lang="ts">
    import Vue from 'vue';
    import { Compact } from 'vue-color';

    export default Vue.extend({
        components: {
            Compact,
        },
        props: {
            color: {
                type: String,
                required: true,
            },
            x: {
                type: Number,
                required: true,
            },
            y: {
                type: Number,
                required: true,
            },
        },
        computed: {
            style() {
                return {
                    position: 'absolute',
                    top: `${(this as any).y}px`,
                    left: `${(this as any).x}px`,
                };
            },
        },
        methods: {
            updateValue(value: any) {
                this.$emit('update:color', value.hex);
                this.$emit('close');
            },
        },
    });
</script>
