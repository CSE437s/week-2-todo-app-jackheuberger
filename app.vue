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
  // if string empty, do nothing
  if (!newTodo.value) return

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
    <div class="prose prose-lg mt-10 justify-center text-center w-1/2">
      <h1 class="text-accent">Todo App</h1>
      <!-- Todos with checkboxes -->
      <div class="card shadow-lg text-center m-0 bg-neutral-50">
        <div class="text-left">
          <ul class="list-disc">
            <li v-for="todo in todos" :key="todo.id">
              <input type="checkbox" v-model="todo.completed" @change="updateTodo(todo)" />
              {{ todo.text }}
            </li>
          </ul>
        </div>
        <div>
          <input type="text" placeholder="Type here" class="input input-bordered w-full max-w-xs" v-model="newTodo" />
        </div>
        <div>
          <button @click="addTodo" class="btn btn-secondary w-1/4 my-2">Add Todo</button>
        </div>
      </div>
    </div>
  </div>
</template>