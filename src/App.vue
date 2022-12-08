<script setup lang="ts">
import { ref, onMounted, computed, watch } from "vue";
import type { Ref } from "vue";

type TodosTypes = {
  content: string;
  category: string;
  done: boolean;
  createdAt: number;
};

const todos: Ref<TodosTypes[]> = ref([]);

const inputContent = ref("");
const inputCategory: Ref<"Business" | "Personal" | null> = ref(null);

const handleMakeTodosAscending = computed(() =>
  todos.value.sort((a, b) => b.createdAt - a.createdAt)
);

const handleAddTodo = () => {
  if (inputContent.value.trim() === "" || inputCategory.value === null) {
    return;
  }

  todos.value.push({
    content: inputContent.value,
    category: inputCategory.value,
    done: false,
    createdAt: new Date().getTime(),
  });

  inputContent.value = "";
  inputCategory.value = null;
};

const handleRemoveTodo = (todo: TodosTypes) => {
  todos.value = todos.value.filter((item) => item !== todo);
};

watch(
  todos,
  (newValue) => {
    console.log(newValue);
    window.localStorage.setItem("todos", JSON.stringify(newValue));
  },
  { deep: true }
);

onMounted(() => {
  todos.value = JSON.parse(window.localStorage.getItem("todos")!) || [];
});
</script>

<template>
  <main class="app">
    <section class="greeting">
      <h2 class="title">What's up?</h2>
    </section>

    <section class="create-todo">
      <form @submit.prevent="handleAddTodo">
        <h4>Pick a category</h4>

        <div class="options">
          <label>
            <input
              type="radio"
              name="category"
              value="business"
              v-model="inputCategory"
            />

            <span class="bubble business" />
            <div>Business</div>
          </label>

          <label>
            <input
              type="radio"
              name="category"
              value="personal"
              v-model="inputCategory"
            />

            <span class="bubble personal" />
            <div>Personal</div>
          </label>
        </div>
        <input
          type="text"
          placeholder="e.g. make a video"
          v-model="inputContent"
        />

        <button type="submit">Add Todo</button>
      </form>
    </section>

    <section class="todo-list" v-if="todos.length">
      <h3>TODO List</h3>

      <div class="list">
        <div
          v-for="todo in handleMakeTodosAscending"
          :class="`todo-item ${todo.done && 'done'}`"
        >
          <label>
            <input type="checkbox" v-model="todo.done" />
            <span :class="`bubble ${todo.category}`" />
          </label>

          <div class="todo-content">
            <input type="text" v-model="todo.content" />
          </div>

          <div class="actions">
            <button class="delete" @click="handleRemoveTodo(todo)">
              Delete
            </button>
          </div>
        </div>
      </div>
    </section>
  </main>
</template>
