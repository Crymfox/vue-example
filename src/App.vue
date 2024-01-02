<script lang="ts">
  import Item from './components/Item.vue'
  import Container from './components/Container.vue'
  import ItemInput from './components/ItemInput.vue'
  import AddButton from './components/AddButton.vue'
  import { ref } from 'vue'
  import axios from 'axios'
  export default {
    data() {
      return {
        items: ref<{key: number, value: string}[]>([]),
        item: ref<string>(''),
        search: ref<string>(''),
        filteredItems: ref<{key: number, value: string}[]>([]),
        loading: ref<boolean>(true),
      }
    },
    mounted() {
      this.loading = true
      axios.get('https://worker.sameur-bh.workers.dev/')
        .then((response: {data: {key: string, value: string}[]}) => {
          this.items = response.data.map((item) => {
            return {
              key: parseInt(item.key),
              value: item.value
            }
          })
          this.items.sort((a, b) => a.key - b.key) // Sort by key
          this.loading = false
        }).catch((error: Error) => {
          console.log(error)
          this.loading = false
        })
    },
    methods: {
      addItem() {
        if (this.item === '') return
        axios.post('https://worker.sameur-bh.workers.dev/post', {
          key: this.items.reduce((max, item) => item.key > max ? item.key : max, 0) + 1 + '',
          value: this.item
        }, {
          headers: {
            "Content-Type": "application/json"
          }
        }).then((response) => {
          console.log(response)
        }).catch((error) => {
          console.log(error)
        })
        this.items.push({
          key: this.items.reduce((max, item) => item.key > max ? item.key : max, 0) + 1,
          value: this.item
        })
      },
      removeItem(item: string) {
        axios.delete('https://worker.sameur-bh.workers.dev/delete', {
          data: {
            key: this.items.find((i) => i.value === item)?.key
          },
          headers: {
            "Content-Type": "application/json"
          }
        }).then((response) => {
          console.log(response)
        }).catch((error) => {
          console.log(error)
        })
        this.items = this.items.filter((i) => i.value !== item)
        this.filteredItems = this.filteredItems.filter((i) => i.value !== item)
      },
      clearItems() {
        if (confirm('Are you sure you want to clear all tasks?')) {
          axios.put('https://worker.sameur-bh.workers.dev/deleteAll', {
            headers: {
              "Content-Type": "application/json"
            }
          })
          .then((response) => {
            console.log(response)
          }).catch((error) => {
            console.log(error)
          })
          this.items = []
        }
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
          return item.value.toLowerCase().includes(this.search.toLowerCase())
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
      <div class="flex flex-wrap gap-20">
        <Container class="">
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
            <div v-if="items.length === 0 || (filteredItems.length === 0 && search !== '')" class="text-2xl my-2 text-gray-500 custom:w-[35rem] w-fit flex flex-col items-center">{{loading ? "Loading..." : "No tasks"}}</div>
            <ul v-else class="my-2">
              <Item v-if="search === ''" v-for="item in items" :item="item.value" @removeFunction="removeItem" />
              <Item v-else v-for="item in filteredItems" :item="item.value" @removeFunction="removeItem" />
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