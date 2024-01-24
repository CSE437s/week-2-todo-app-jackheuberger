<script setup>
const client = useSupabaseClient()
const todos = ref([])
const newTodo = ref('')

async function getTodos() {
  // Get open todos
  const { data } = await client.from('todos').select().order('created_at', { ascending: true })
  todos.value = data
}

async function addTodo() {
  const { data } = await client.from('todos').insert([
    { text: newTodo.value }
  ])

  //rerender component & reset box
  getTodos()
  newTodo.value = ''
}

async function updateTodo(todo) {
  // Update todo
  const { data } = await client.from('todos').update({
    completed: todo.completed
  }).eq('id', todo.id).select()
}

getTodos()

</script>

<template>
  <div class="flex justify-center">
    <div class="prose prose-lg mt-10 justify-center text-center">
      <h1>Todo App</h1>
      <h2>Open Todos</h2>
      <!-- Todos with checkboxes -->
      <div class="card w-96 shadow-lg mx-auto text-center">
        <ul>
          <li v-for="todo in todos" :key="todo.id">
            <input type="checkbox" v-model="todo.completed" @change="updateTodo(todo)" />
            {{ todo.text }}
          </li>
        </ul>

        <input type="text" v-model="newTodo" />
        <button @click="addTodo" class="btn-xs">Add Todo</button>
      </div>
    </div>
  </div>
</template>