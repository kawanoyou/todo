<template>
  <div id="app">
    <p>
      <input type="text"
      placeholder="TODOを追加"
      v-model="newTodoText"
      @keyup.enter="addTodo">
    </p>
    <ul>
      <li v-for="(todo, i) in filteredTodos" :key="i">
        <label :class="{ done: todo.isComplete }">
          <input type="checkbox" v-model="todo.isComplete" @change="saveTodo">{{ todo.text }}
        </label>
        <button v-if="todo.isComplete" @click="deleteTodo(i)">x</button>
      </li>
    </ul>
    <button @click="deleteCompletedTodo" v-if="completedTodoExists">完了したTODOを削除</button>
    <p>未完了のTODO {{ activeTodoCount }}個</p>
    <p>
      <label><input type="radio" value="all" v-model="filter">すべて</label>
      <label><input type="radio" value="active" v-model="filter">未完了</label>
      <label><input type="radio" value="complete" v-model="filter">完了</label>
    </p>
  </div>
</template>

<script>
export default {
  name: 'App',
  data: function() {
    return {
      newTodoText: '',
      todos: [],
      activeTodoCount: 0,
      filter: 'all',
    }
  },
  computed: {
    filteredTodos: function() {
      if (this.filter === 'active') {
        return this.todos.filter(todo => !todo.isComplete);
      } else if (this.filter === 'complete') {
        return this.todos.filter(todo => todo.isComplete);
      }
      return this.todos;
    },
    completedTodoExists: function() {
      return this.todos.filter(todo => todo.isComplete).length > 0;
    }
  },
  watch: {
    todos: function() {
      this.countActiveTodo();
    }
  },
  methods: {
    countActiveTodo: function(){
      this.activeTodoCount = this.todos.filter(todo => todo.isComplete === false).length
    },
    addTodo: function() {
      this.todos.push({
        text: this.newTodoText,
        isComplete: false,
      });
      this.newTodoText = '';
      this.saveTodo();
      this.countActiveTodo();
    },
    deleteTodo: function(i) {
      this.todos.splice(i, 1)
      this.saveTodo();
    },
    deleteCompletedTodo: function() {
      this.todos = this.todos.filter(function(item){
        return item.isComplete === false;
      });
      this.saveTodo();
    },
    saveTodo: function() {
      localStorage.setItem('items', JSON.stringify(this.todos))
      this.countActiveTodo();
    },
    loadTodo: function() {
      this.todos = JSON.parse(localStorage.getItem('items'))
      if (!this.todos) {
        this.todos = [];
      }
    },
    incrementTotal: function() {
      console.log('incrementTotal')
      this.total += 1
    },
  },
  mounted: function() {
    this.loadTodo();
  }
}
</script>

<style scoped>
.done { text-decoration: line-through; }
</style>