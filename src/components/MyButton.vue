<template>
    <button :class="['btn', color, {flash: flash}]" @click="handleClick"></button>
</template>

<script>
export default {
    name: 'my-button',
    props: {
        index: {type: Number},
        color: {type: String},
        flash: {type: Boolean}
    },
    methods: {
        handleClick() {
            this.$emit('on-btn-cliked', this.index)
        }
    }
}
</script>