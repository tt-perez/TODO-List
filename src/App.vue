
<script setup>
import { onMounted, ref } from "vue";

const content = ref("");
const todos = ref([]);
const currentDate = ref("");

const formatDate = (date) => {
  const options = { day: 'numeric', month: 'long', year: 'numeric' };
  return date.toLocaleDateString('en-EN', options);
};
onMounted(() => {
  const storedTodos = localStorage.getItem("todos");
  if (storedTodos) {
    todos.value = JSON.parse(storedTodos);
  }
  const date = new Date();
  currentDate.value = formatDate(date);
});

const addTodo = () => {
  if (content.value.trim() === '') {
    return;
  }
  todos.value.push({
    content: content.value,
    done: false,
    editable: false,
    createdAt: new Date().getTime()
  });
  content.value = ""; 
  saveTodos();
};

const deleteTodo = (index) => {
  todos.value.splice(index, 1); 
  saveTodos();
};
const saveTodos = () => {
  localStorage.setItem("todos", JSON.stringify(todos.value));
};

</script>

<template>
  <v-responsive class="border rounded" max-height="100%">
    <v-app>
      <div class="app-bar">
        <p class="welcome">Welcome back, Tay!</p>
        <h2 class="title">TODO List</h2>
        <p class="remind">Remind everything</p>
        <p class="date">{{ currentDate }}</p>
      </div>

      <v-main class="main">
        <v-container>
          <div class="tasks">
            <h3>Daily tasks</h3>
            <v-form @submit.prevent="addTodo">
              <v-text-field
                class="input"
                label="Enter your task"
                variant="underlined"
                v-model="content"
              ></v-text-field>
              <v-btn
                @click="addTodo"
                class="add"
                color="primary"
                size="small"
              >
                ADD
                <v-icon icon="mdi-checkbox-marked-circle" end></v-icon>
              </v-btn>
            </v-form>

            <div class="list" :style="{ maxHeight: '300px', overflowY: 'auto' }">
              <p class="todo">TODO List</p>
              <div v-for="(todo, index) in todos" :key="todo.createdAt" class="list-tasks">
                <div class="task-item">
                  <input type="checkbox" class="check" v-model="todo.done" />
                  <span :class="{ 'ticked': todo.done }" class="name">{{ todo.content }}</span>
                  <v-btn @click="deleteTodo(index)" size="x-small" class="delete" color="red">
                    Delete
                    <v-icon icon="mdi-cancel" end></v-icon>
                  </v-btn>
                </div>
              </div>
            </div>
          </div>
        </v-container>
      </v-main>
    </v-app>
  </v-responsive>
</template>

