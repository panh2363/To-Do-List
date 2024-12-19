<template>
  <div id="app">
    <TodoList></todolist>
  </div>
</template>

<script>

import TodoList from './components/TodoList.vue';

export default {
  name: 'App',
  components: {
    TodoList
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #3d6994;
  margin-top: 30px;
  background-color: #f4f7f6; /* Màu nền nhẹ nhàng */
  min-height: 100vh;
}

.container {
  max-width: 600px;
  margin: auto;
  padding: 50px;
  background-color: #fff;
  border-radius: 12px; /* Bo góc mềm mại */
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.2); /* Đổ bóng nhẹ */
}

h1 {
  font-size: 2rem;
  color: #34495e;
  margin-bottom: 40px;
}

button {
  background-color: #2980b9;
  color: #fff;
  border: none;
  padding: 10px 15px;
  border-radius: 6px;
  cursor: pointer;
  transition: background 0.3s ease;
}

button:hover {
  background-color: #3498db;
}

input {
  width: calc(50% - 30px);
  padding: 10px;
  border: 1px solid #ccc;
  border-radius: 6px;
  margin-bottom: 15px;
  margin-right: 10px;
  box-sizing: border-box;
}

</style>
