<script setup>
import { createClient } from '@supabase/supabase-js'
const supabase = createClient('https://snljomcuwslsnlyxyvup.supabase.co', 'eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJzdXBhYmFzZSIsInJlZiI6InNubGpvbWN1d3Nsc25seXh5dnVwIiwicm9sZSI6ImFub24iLCJpYXQiOjE3MDYwNTMzMTksImV4cCI6MjAyMTYyOTMxOX0.eR7by00dpwO9CqsO9L2_HukEbgwgzo9JHAfoRoXF5wE')
const todos = ref([])
const newTodo = ref('')

async function getTodos() {
  // Get open todos
  const { data } = await supabase.from('todos').select().order('created_at', { ascending: false })
  todos.value = data
}

async function addTodo() {
  const { data } = await supabase.from('todos').insert([
    { text: newTodo.value }
  ])

  //rerender component & reset box
  getTodos()
  newTodo.value = ''
}

async function updateTodo(todo) {
  console.log("Updating todo", todo.id, "with text", todo.text, "and completed", todo.completed)
  // Update todo
  const { data } = await supabase.from('todos').update({
    completed: todo.completed
  }).eq('id', todo.id).select()
}

onMounted(() => {
  getTodos()
})
</script>

<template>
  <h1>Todo App</h1>
  <h2>Open Todos</h2>
  <!-- Todos with checkboxes -->
  <ul>
    <li v-for="todo in todos" :key="todo.id">
      <input type="checkbox" v-model="todo.completed" @change="updateTodo(todo)"/>
      {{ todo.text }}
    </li>
  </ul>

  <input type="text" v-model="newTodo" />
  <button @click="addTodo">Add Todo</button>
  
</template>