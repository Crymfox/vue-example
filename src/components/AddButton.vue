<script lang="ts">
export default {
    mounted() {
        window.addEventListener('keydown', (e) => {
            if (e.key === 'Enter') {
                this.onClick();
            }
        })
    },
    props: {
        addFunction: {
            type: Function,
            required: true
        }
    },
    methods: {
        onClick() {
            this.addFunction();
            this.$emit('clearInput');
        }
    }
}
</script>

<template>
    <button class="
        bg-blue-500
        hover:bg-blue-700
        text-white
        font-bold
        py-2
        px-4
        rounded
    " @click="onClick()">Add</button>
</template>