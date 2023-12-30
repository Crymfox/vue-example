<script lang="ts">
export default {
    props: {
        item: {
            type: String,
            required: true
        }
    },
    methods: {
        handleInput(e: Event) {
            const newValue = (e.target as HTMLInputElement).value;
            this.$emit('updateInput', newValue);
        },
        
    }
}
</script>

<template>
    <input type="text" id="input" :value="item" class="
        border-2
        border-gray-300
        bg-white
        h-10
        px-5
        pr-16
        rounded-lg
        text-sm
        focus:outline-none
        my-5
    "
    placeholder="Add new item" @input="handleInput">
</template>