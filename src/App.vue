<script lang="ts">
  import Item from './components/Item.vue'
  import Container from './components/Container.vue'
  import ItemInput from './components/ItemInput.vue'
  import AddButton from './components/AddButton.vue'
  import { ref } from 'vue'
  export default {
    data() {
      return {
        items: ref<string[]>([]),
        item: ref<string>(''),
        search: ref<string>(''),
        filteredItems: ref<string[]>([])
      }
    },
    methods: {
      addItem() {
        if (this.item === '') return
        this.items.push(this.item)
      },
      removeItem(item: string) {
        this.items = this.items.filter((i) => i !== item)
        this.filteredItems = this.filteredItems.filter((i) => i !== item)
      },
      clearItems() {
        if (confirm('Are you sure you want to clear all tasks?')) this.items = []
      },
      updateInputValue(newValue: string) {
        this.item = newValue
      },
      clearInput() {
        this.item = ''
      },
      handleSearch(e: Event) {
        const newValue = (e.target as HTMLInputElement).value
        this.search = newValue
        this.filteredItems = this.items.filter((item) => {
          return item.toLowerCase().includes(this.search.toLowerCase())
        })
      },
    },
    components: {
      Item,
      Container,
      ItemInput,
      AddButton,
    }
  }

</script>

<template>
  <Container>
    <template v-slot:child>
      <div class="flex gap-20">
        <Container>
          <template v-slot:child>
            <h1 class="text-3xl">New Task</h1>
            <ItemInput @updateInput="updateInputValue" :item="item" />
            <AddButton :addFunction="addItem" @clearInput="clearInput" />
            <input type="search" placeholder="Search..." :value="search" @input="handleSearch" class="
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
            ">
          </template>
        </Container>
        <Container>
          <template v-slot:child>
            <h1 class="text-3xl">Tasks</h1>
            <div v-if="items.length === 0 || (filteredItems.length === 0 && search !== '')" class="text-2xl my-2 text-gray-500 w-[35rem] flex flex-col items-center">No tasks</div>
            <ul v-else class="my-2">
              <Item v-if="search === ''" v-for="item in items" :item="item" @removeFunction="removeItem" />
              <Item v-else v-for="item in filteredItems" :item="item" @removeFunction="removeItem" />
            </ul>
            <button class="
              bg-red-500
              hover:bg-red-700
              text-white
              font-bold
              py-2
              px-4
              rounded
              my-2
            " @click="clearItems">Clear</button>
          </template>
        </Container>
      </div>
    </template>
  </Container>
</template>