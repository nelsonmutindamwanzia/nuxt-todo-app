<template>
  <div>
    <h1>New To-Do</h1>
    <form @submit.prevent="addTodo()">
      <label>New To-do </label>
      <input v-model="newTodo" name="newTodo" autocomplete="off" />
      <button>Add To-do</button>
    </form>
    <h2>To-do List</h2>
    <ul>
      <li v-for="(todo, index) in todos" :key="index">
        <span
          v-if="todo.id !== editingTodoId"
          :class="{ done: todo.done }"
          @click="toggleEdit(todo)"
          >{{ todo.content }}</span
        >
        <input v-else v-model="editedTodoContent" />
        <button v-if="todo.id !== editingTodoId" @click="editTodo(todo)">
          Edit
        </button>
        <button v-else @click="saveTodo()">Save</button>
        <button @click="removeTodo(index)">Remove</button>
      </li>
    </ul>
    <h4 v-if="todos.length === 0">Empty list.</h4>
  </div>
</template>

<script>
import { ref, onMounted } from 'vue';

export default {
  name: 'app',
  setup() {
    const newTodo = ref('');
    const defaultData = [
      {
        id: 1,
        done: false,
        content:
          'Write a blog post. Click the "remove" button to delete an item.',
      },
    ];
    const todos = ref([]);
    const editingTodoId = ref(null);
    const editedTodoContent = ref('');

    onMounted(() => {
      if (typeof window !== 'undefined') {
        const todosData =
          JSON.parse(localStorage.getItem('todos')) || defaultData;
        todos.value = todosData;
      }
    });

    function addTodo() {
      if (newTodo.value) {
        const newId = todos.value.length
          ? todos.value[todos.value.length - 1].id + 1
          : 1;
        todos.value.push({
          id: newId,
          done: false,
          content: newTodo.value,
        });
        newTodo.value = '';
        saveData();
      }
    }

    function editTodo(todo) {
      editingTodoId.value = todo.id;
      editedTodoContent.value = todo.content;
    }

    function saveTodo() {
      const index = todos.value.findIndex(
        (todo) => todo.id === editingTodoId.value
      );
      if (index !== -1) {
        todos.value[index].content = editedTodoContent.value;
        editingTodoId.value = null;
        saveData();
      }
    }

    function removeTodo(index) {
      todos.value.splice(index, 1);
      saveData();
    }

    function toggleEdit(todo) {
      if (editingTodoId.value !== todo.id) {
        editingTodoId.value = todo.id;
        editedTodoContent.value = todo.content;
      }
    }

    function saveData() {
      if (typeof window !== 'undefined') {
        const storageData = JSON.stringify(todos.value);
        localStorage.setItem('todos', storageData);
      }
    }

    return {
      todos,
      newTodo,
      addTodo,
      removeTodo,
      toggleEdit,
      editingTodoId,
      editedTodoContent,
      saveTodo,
      editTodo, // expose editTodo function
    };
  },
};
</script>

<style lang="scss">
$border: 2px solid
  rgba(
    $color: white,
    $alpha: 0.35,
  );
$size1: 6px;
$size2: 12px;
$size3: 18px;
$size4: 24px;
$size5: 48px;
$backgroundColor: #122d42;
$textColor: white;
$primaryColor: #a0a4d9;
$secondTextColor: #1f2023;

body {
  margin: 0;
  padding: 10px;
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  background-color: $backgroundColor;
  color: $textColor;
  max-width: 600px;
  margin-left: auto;
  margin-right: auto;
  padding: 20px;
  h1 {
    font-weight: bold;
    font-size: 28px;
    text-align: center;
  }
  form {
    display: flex;
    flex-direction: column;
    width: 100%;
    label {
      font-size: 14px;
      font-weight: bold;
    }
    input,
    button {
      height: $size5;
      box-shadow: none;
      outline: none;
      padding-left: $size2;
      padding-right: $size2;
      border-radius: $size1;
      font-size: 18px;
      margin-top: $size1;
      margin-bottom: $size2;
      display: wrap;
    }
    input {
      background-color: transparent;
      border: $border;
      color: inherit;
    }
  }
  button {
    cursor: pointer;
    background-color: #3ed1c9;
    border: 1px solid #3ed1c9;
    color: $secondTextColor;
    font-weight: bold;
    outline: none;
    border-radius: $size1;
  }
  h2 {
    font-size: 22px;
    border-bottom: $border;
    padding-bottom: $size1;
  }
  ul {
    padding: 10px;
    li {
      display: flex;
      justify-content: space-between;
      align-items: center;
      border: $border;
      padding: $size2 $size4;
      border-radius: $size1;
      margin-bottom: $size2;
      span {
        cursor: pointer;
      }
      input {
        background-color: transparent;
        border: $border;
        color: inherit;
      }
      .done {
        text-decoration: line-through;
      }
      button {
        font-size: $size2;
        padding: $size1;
      }
    }
  }
  h4 {
    text-align: center;
    opacity: 0.5;
    margin: 0;
  }
}
</style>