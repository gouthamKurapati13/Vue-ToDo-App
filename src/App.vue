<script setup>
  import { ref, onMounted, computed, watch } from 'vue'

  const todos = ref([])
  const name = ref('')

  const input_content = ref('')
  const input_category = ref(null)

  const addToDo = () => {
    if (input_content.value.trim() && input_category.value) {
      todos.value.push({
        content: input_content.value.trim(),
        category: input_category.value,
        done: false,
        createdAt: Date.now()
      })
      input_content.value = ''
      input_category.value = null
    }
  }

  const removeToDo = (todo) => {
    const index = todos.value.indexOf(todo)
    todos.value.splice(index, 1)
  }

  watch(todos, (newVal) => {
    localStorage.setItem('todos', JSON.stringify(newVal));
  }, { deep: true })

  const todos_asc = computed(() => todos.value.sort((a, b) => a.createdAt - b.createdAt))

  watch(name, (newVal) => {
    localStorage.setItem('name', newVal);
  })

  onMounted(() => {
    name.value = localStorage.getItem('name') || '';
    todos.value = JSON.parse(localStorage.getItem('todos')) || [];
  })

</script>

<template>

  <main class="app">
    <section class="greeting">
      <h2 class="title">Hello, <input type="text" placeholder="Name here" v-model="name" /></h2>
    </section>

    <section class="create-todo">
      <form @submit.prevent="addToDo">
        <input type="text" v-model="input_content" placeholder="Add a new item to the list" />
        <h4>Pick a category</h4>
        <div class="options">
          <label>
            <input type="radio" name="category" value="business" v-model="input_category"/>
            <span class="bubble business"></span>
            <div>Business</div>
          </label>
          <label>
            <input type="radio" name="category" value="personal" v-model="input_category"/>
            <span class="bubble personal"></span>
            <div>Personal</div>
          </label>
        </div>
        <input type="submit" value="Add Todo" />
      </form>
    </section>

    <section class="todo-list">
      <div class="list">
        <div v-for="todo in todos_asc" :class="`todo-item ${todo.done && 'done'}`">
          <label>
            <input type="checkbox" v-model="todo.done" />
            <span :class="`bubble ${todo.category}`"></span>
          </label>

          <div class="todo-content">
            <input type="text" v-model="todo.content"/>
          </div>

          <div class="actions">
            <button class="delete" @click="removeToDo(todo)">Delete</button>
          </div>

        </div>
      </div>
    </section>

  </main>
  
</template>
