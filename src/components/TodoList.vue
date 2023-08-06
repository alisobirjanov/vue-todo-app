<template>
  <div>
    <form @submit.prevent="addTodo">
      <div class="flex gap-1">
        <input 
          type="text"
          placeholder="Enter todo..."
          class="p-2 w-full border-2 rounded-lg outline-none bg-neutral-900 border-neutral-900 focus:border-blue-500"
          v-model="text"
        />
        <button 
          class="py-2 px-4 rounded-lg bg-neutral-900"
          @click="addTodo"
        >
          Add
        </button>
      </div>
    </form>
    <TransitionGroup tag="ul">
      <li
        v-for="(todo, index) in todos" :key="todo.id" 
        class="p-2.5 mt-2 bg-neutral-900 rounded-lg flex justify-between"
      >
        <div class="max-w-full">
          <input type="checkbox" v-model="todos[index].done" @input="updateTodo">
          <span 
            class="pl-2 break-words"
            :class="{'line-through': todo.done}"
          >
            {{ todo.text }}
          </span>
        </div>
        <span 
          class="cursor-pointer select-none" 
          title="Delete" 
          @click="deleteTodo(todo.id)"
        >
          &cross;
        </span>
      </li>
    </TransitionGroup>
  </div>
</template>

<script setup lang="ts">
import { ref } from 'vue'

interface Todo {
  text: string,
  done: boolean,
  id: number
}

const text = ref('')
const todos = ref<Todo[]>([])

const LOCAL_STORAGE_KEY = 'todos'

const addTodo = () => {
  if(!text.value) return
  todos.value.push({ 
    text: text.value, 
    done: false,
    id: Date.now()
  })
  text.value = ''
  saveToLocalStorage()
}

const deleteTodo = (id: number) => {
  todos.value = todos.value.filter((todo) => todo.id !== id)
  saveToLocalStorage()
}

const updateTodo = () => {
  setTimeout(() => {
    saveToLocalStorage()
  }, 1)
}


const saveToLocalStorage = () => {
  localStorage.setItem(LOCAL_STORAGE_KEY, JSON.stringify(todos.value))
}

try {
  const data = localStorage.getItem(LOCAL_STORAGE_KEY) || [] as any
  const jsonData = JSON.parse(data)
  if(Array.isArray(jsonData)) {
    todos.value = jsonData
  }
} catch(err) {
  console.log(err)
}
</script>

<style>
.v-enter-active,
.v-leave-active {
  transition: all 0.5s ease;
}
.v-leave-to {
  opacity: 0;
  transform: translateX(30px);
}
.v-enter-from {
  opacity: 0;
} 
</style>