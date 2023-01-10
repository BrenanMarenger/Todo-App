<script setup>
import { ref, onMounted, computed, watch } from 'vue'


const todos = ref([])
const user = ref('')

const input_content = ref('')
const input_category = ref(null) //add a filter option

// const filters = computed(() => {
//   return todos.filter
// })
//Adds new items to top of the list
const todos_asc = computed(() => todos.value.sort((a, b) => {
  return b.createdAt - a.createdAt
}))

const addTodo = () => {
  if (input_content.value.trim() === '' || input_category.value === null) {
    return
  }

  todos.value.push({
    content: input_content.value,
    category: input_category.value,
    createdAt: new Date().getTime(), //Display on note
    done: false
  })

  input_content.value = ''
  input_category.value = null

}

const removeTodo = todo => {
  todos.value = todos.value.filter(t => t !== todo)
}

const displayImportant = () => {
  var importantCheckBox = document.getElementById('filterImportant')
  if (importantCheckBox.checked != true) {
    console.log("Do not show the important")
  } else {
    console.log("show importants")
  }
}

const displayMiscellaneous = () => {
  var miscellaneousCheckBox = document.getElementById('filterMiscellaneous')
  if (miscellaneousCheckBox.checked != true) {
    //display none
  }
  //display
}

watch(todos, newVal => {
  localStorage.setItem('todos', JSON.stringify(newVal))
}, { deep: true })

watch(user, (newVal) => {
  localStorage.setItem('user', newVal)
})

onMounted(() => {
  user.value = localStorage.getItem('user') || ''
  todos.value = JSON.parse(localStorage.getItem('todos')) || []
})

</script>

<template>
  <main class="app">

    <section class="greeting">
      <h2 class="title">
        Welcome, <input type="text" placeholder="Name here" v-model="user" />
      </h2>
    </section>

    <section class="create-todo">
      <h3>CREATE A TODO</h3>

      <h4>Add a new task</h4>
      <form @submit.prevent="addTodo">
        <input type="text" placeholder=" e.g. read my book" v-model="input_content" />

        <h4>Pick a category</h4>
        <div class="options">
          <label>
            <input type="radio" name="category" value="important" v-model="input_category" />

            <span class="bubble important"></span>
            <div>Important</div>
          </label>

          <label>
            <input type="radio" name="category" value="miscellaneous" v-model="input_category" />

            <span class="bubble miscellaneous"></span>
            <div>Miscellaneous</div>
          </label>
        </div>

        <input type="submit" value="Add Todo">
      </form>
    </section>

    <section class="todo-list">
      <h3>TODO LIST</h3>
      <div>
        <h4>
          Filter
        </h4>
        <label>
          <input type="checkbox" name="category" value="important" checked @change="displayImportant()"
            id="filterImportant" />
          <span class="bubble important"></span>

        </label>
        <br>
        <label>
          <input type="checkbox" name="category" value="miscellaneous" checked @change="displayMiscellaneous()"
            id="filterMiscellaneous" />
          <span class="bubble miscellaneous"></span>

        </label>
      </div>
      <div class="list">
        <div v-for="todo in todos_asc" :class="`todo-item ${todo.done && 'done'}`">
          <label>
            <input type="checkbox" v-model="todo.done" />
            <span :class="`bubble ${todo.category}`"></span>
          </label>

          <div class="todo-content">
            <input type="text" v-model="todo.content" />
          </div>

          <div class="actions">
            <button class="delete" @click="removeTodo(todo)">Delete</button>
          </div>

        </div>
      </div>
    </section>
  </main>
</template>


