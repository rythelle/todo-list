<script setup>
import { ref, onMounted, computed, watch } from 'vue';

const todoList = ref([]);
const name = ref('');

const inputContent = ref('');
const inputCategory = ref(null);

const todoListAsc = computed(() =>
  todoList.value.sort((a, b) => {
    return b.createdAt - a.createdAt;
  }),
);

watch(name, (newValue) => {
  localStorage.setItem('name', newValue);
});

onMounted(() => {
  name.value = localStorage.getItem('name') || '';

  todoList.value = JSON.parse(localStorage.getItem('todoList')) || [];
});

const addTodo = () => {
  if (inputContent.value.trim() === '' || inputCategory.value == null) return;

  todoList.value.push({
    content: inputContent.value,
    category: inputCategory.value,
    done: false,
    createdAt: new Date().getTime(),
  });

  inputCategory.value = null;
  inputContent.value = '';
};

watch(
  todoList,
  (newValue) => {
    localStorage.setItem('todoList', JSON.stringify(newValue));
  },
  { deep: true },
);

const removeTodo = (todo) => {
  todoList.value = todoList.value.filter((todoToRemove) => todoToRemove !== todo);
};
</script>

<template>
  <main class="app">
    <section class="greeting">
      <h2 class="title">
        Olá, tudo bem ?
        <input
          type="text"
          placeholder="Seu nome aqui"
          v-model="name" />
      </h2>
    </section>

    <section class="create-todo">
      <h3>Criar uma tarefa</h3>

      <form @submit.prevent="addTodo">
        <h4>Qual é a sua terefa?</h4>
        <input
          type="text"
          placeholder="Exemplo: comprar café"
          v-model="inputContent" />
        <h4>Escolha uma categoria</h4>

        <div class="options">
          <label>
            <input
              type="radio"
              name="category"
              value="business"
              v-model="inputCategory" />
            <span class="bubble business" />
            <div>Trabalho</div>
          </label>

          <label>
            <input
              type="radio"
              name="category"
              value="personal"
              v-model="inputCategory" />
            <span class="bubble personal" />
            <div>Pessoal</div>
          </label>
        </div>

        <input
          type="submit"
          value="Adicionar tarefa" />
      </form>
    </section>

    <section class="todo-list">
      <h3>Lista de tarefas</h3>
      <div class="list">
        <div
          v-for="todo in todoListAsc"
          :key="todo.createdAt"
          :class="`todo-item ${todo.done && 'done'}`">
          <label>
            <input
              type="checkbox"
              v-model="todo.done" />
            <span :class="`bubble ${todo.category}`" />
          </label>

          <div class="todo-content">
            <input
              type="text"
              v-model="todo.content" />
          </div>

          <div class="actions">
            <button
              class="delete"
              @click="removeTodo(todo)"
              >Delete</button
            >
          </div>
        </div>
      </div>
    </section>
  </main>
</template>

<style scoped></style>
