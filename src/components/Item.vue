<script lang="ts">
export default {
    mounted() {
        window.addEventListener('keydown', (e) => {
            if (e.key === 'Escape') {
                this.isSelected = false;
            }
        })
    },
    props: {
        item: {
            type: String,
            required: true
        }
    },
    data() {
        return {
            isSelected: false
        }
    },
    methods: {
        selectItem() {
            this.isSelected = !this.isSelected;
        },
        removeItem() {
            this.$emit('removeFunction', this.item);
        }
    }
}
</script>

<template>
    <li :class="{
        'bg-blue-300': isSelected,
        'flex-col items-center hover:bg-blue-100': !isSelected
    }" class="flex text-2xl items-center list-disc w-[35rem] cursor-pointer" @click="selectItem">
        <span>{{ item }}</span>
        <button v-if="isSelected" class="
            bg-red-500
            hover:bg-red-700
            text-white
            font-bold
            py-1
            px-4
            rounded
            text-sm
            ml-auto
        " @click="removeItem">Remove</button>
    </li>
</template>
