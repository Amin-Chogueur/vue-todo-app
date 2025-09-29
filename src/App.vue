<script setup>
import { ref, watch, onMounted, computed } from "vue";
const name = ref("");

const todos = ref([]);
const title = ref("");
const category = ref(null);
const filter = ref("to-do");

function addTodo() {
  if (!title.value.trim() || !category.value) {
    alert("Please fill out all the info");
  }
  const newTodo = {
    id: Date.now(),
    title: title.value,
    category: category.value,
    completed: false,
  };
  todos.value.unshift(newTodo);
  title.value = "";
  category.value = null;
}

const filtredTodos = computed(() => {
  return todos.value.filter((todo) =>
    filter.value === "to-do"
      ? todo.completed === false
      : todo.completed === true
  );
});

function deleteTodo(id) {
  const confirm = window.confirm("Are you sure you want to delet this task?");
  if (confirm) {
    todos.value = todos.value.filter((todo) => todo.id !== id);
  }
}
watch(name, (newName) => {
  localStorage.setItem("name", newName);
});
watch(
  todos,
  (newTodos) => {
    localStorage.setItem("vue-todos", JSON.stringify(newTodos));
  },
  { deep: true }
);
onMounted(() => {
  name.value = localStorage.getItem("name") || "";
  todos.value = JSON.parse(localStorage.getItem("vue-todos")) || [];
});
console.log(filtredTodos);
</script>

<template>
  <main>
    <div class="app">
      <section class="greeting">
        <h2 class="title">
          What's up,
          <input type="text" id="name" placeholder="Name here" v-model="name" />
        </h2>
      </section>

      <section class="create-todo">
        <h3>CREATE A TODO</h3>

        <form id="new-todo-form" @submit.prevent="addTodo">
          <h4>What's on your todo list?</h4>
          <input
            type="text"
            name="content"
            id="content"
            placeholder="e.g. make a video"
            v-model="title"
          />

          <h4>Pick a category</h4>
          <div class="options">
            <label>
              <input
                type="radio"
                name="category"
                id="category1"
                value="business"
                v-model="category"
              />
              <span class="bubble business"></span>
              <div>Business</div>
            </label>

            <label>
              <input
                type="radio"
                name="category"
                id="category2"
                value="personal"
                v-model="category"
              />
              <span class="bubble personal"></span>
              <div>Personal</div>
            </label>
          </div>

          <input type="submit" value="Add todo" />
        </form>
      </section>

      <section class="todo-list">
        <h3>TODO LIST {{ filter }}</h3>
        <div class="flex items-center gap-2">
          <button
            @click="
              () => {
                filter = 'to-do';
              }
            "
            class="actions filter"
            :class="filter === 'to-do' ? 'active' : ''"
          >
            TO-DO
          </button>
          <button
            class="actions filter"
            :class="filter === 'done' ? 'active' : ''"
            @click="
              () => {
                filter = 'done';
              }
            "
          >
            DONE
          </button>
        </div>
        <div class="list" id="todo-list">
          <div v-for="todo in filtredTodos" :key="todo.id" class="todo-item">
            <label>
              <input type="checkbox" v-model="todo.completed" />
              <span :class="`bubble ${todo.category}`"></span>
            </label>

            <div class="todo-content">
              <input type="text" v-model="todo.title" />
            </div>

            <div class="actions" @click="deleteTodo(todo.id)">
              <button class="delete">Delete</button>
            </div>
          </div>
        </div>
      </section>
    </div>
  </main>
</template>

<style scoped></style>
