<template>
  <div class="container">
    <div style="flex: 1;">
      <div class="input__div">
        <div class="input__wrapper">
          <input type="text" placeholder="Todo Item" v-model="newTodo" @keyup.enter="addTodo" />
          <button class="flex-no-shrink" type="button" @click="addTodo"> Add </button>
        </div>
        <div>
          <button class="cancel-button" type="button" @click="addTodo"> Cancal </button>
        </div>
      <div class="border"></div>
    </div>

      <div class="todo-list">
        <!-- Loop Over All Todos -->
        <div v-for="(todo, id) in todos" :key="id" class="list">
          <!-- Show Label -->
          <label class="material-checkbox">
            <input type="checkbox" @click="updateTodo(id)" v-model="todo.completed">
            <span></span>
          </label>
          <div class="list-menu">
            <button class="delete" @click="deleteTodo(id)">x</button>
          </div>
          <div class="text" :class="{completed: todo.completed}" v-if="!todo.editing" @dblclick="todo.editing = true; editTodoCache=todo.text">{{ todo.text }}</div>
          <!-- Show Text Box -->
          <div class="input__div" v-else>
            <div class="input__wrapper">
              <input type="text" v-focus v-model="todo.text" @keyup.enter="editTodo(todo)" @keyup.esc="cancelEdit(todo)" @blur="cancelEdit(todo)"/>
            </div>
            <div class="border"></div>
          </div>
      </div>
      </div>
    </div>
    <footer>
      <span>{{ remaining }} items left.</span>
      <div class="delete-button">
        <button class="Delete" type="button" @click="removeCompleted"> Clear All </button>
      </div> 
    </footer>
  </div>
</template>

<script>
export default {
  name: "TodoList",

  data() {
    return {
      newTodo: "",
      editTodoCache: "",
      todos: [
       
      ]
    };
  },

  computed: {
    remaining() {
      return this.todos.filter(todo => !todo.completed).length;
    }
  },

  created() {
    this.todos = JSON.parse(localStorage.getItem("todo")) || []
  },

  methods: {
    addTodo() {
      if (this.newTodo.trim() == "") return;
      let todo = {
        text: this.newTodo,
        completed: false,
        editing: false
      };
      this.todos.push(todo);
      console.log(this.todos)
      this.newTodo = "";
      this.updateLocalStorage();
    },
    editTodo(todo) {
      if (todo.text.trim() == "") todo.text = this.editTodoCache;
      todo.editing = false;
    },
    cancelEdit(todo) {
      todo.text = this.editTodoCache;
      todo.editing = false;
    },

    updateLocalStorage() {
      localStorage.setItem("todo", JSON.stringify(this.todos))
    },
    updateTodo(id) {
      this.todos[id].completed = !this.todos[id].completed;
      this.updateLocalStorage();
    },

    deleteTodo(id) {
      this.todos[id].completed = !this.todos[id].completed;
      this.updateLocalStorage();
    },

    removeCompleted(){
      this.todos = this.todos.filter(todo => !todo.completed)
      this.updateLocalStorage();
    }

  },

  directives: {
    focus: {
      inserted(el) {
        el.focus();
      }
    }
  }
};
</script>

<style scoped>

.container {
  min-height: 300px;
  display: flex;
  flex-direction: column;
}

footer {
  padding: 8px 15px;
  background: #76dbae;
  border-radius: 3px;
  display: flex;
  justify-content: space-between;
}

.todo-list {
  padding: 8px 0;
}

.flex-no-shrink{
  position: absolute;
  right: 55px;
  background-color: #76dbae;
  top:0em;
  height: 35px;
  z-index: 0;
  color: #000;
}

.cancel-button {
  position: absolute;
  right: 0px;
  background-color: red;
  top:0em;
  height: 35px;
  z-index: 0;
}

.list-menu {
  position:absolute;
  right:0px;
  top:1em;
  background-color:yellow;
  z-index:0;
}

.delete-button .Delete {
  background-color: #76dbae;
}

.todo-list .list:hover {
  background: #f7fcfa;
}

.todo-list .list {
  display: flex;
  align-items: center;
  padding: 0 15px;
  position: relative;
}

.todo-list .list .text {
  padding: 0 8px;
  height: 35px;
  line-height: 35px;
  margin: 6px 0;
  cursor: default;
  flex: 1;
}

.todo-list .list .text.completed {
  text-decoration: line-through;
}

.input__div {
  margin: 6px 0;
  position: relative;
  border: 1px solid #e4f5ef;
  flex: 1;
}

.input__div:focus-within .input__wrapper {
  background: #fff;
}

.input__div .input__wrapper {
  background: #f7fcfa;
  padding: 0 15px;
  transition: background 0.3s;
}

.input__div .input__wrapper input {
  height: 35px;
  background: 0 0;
  border: none;
  color: #2c3e50;
  display: block;
  font-family: inherit;
  font-size: 16px;
  line-height: 16px;
  outline: 0;
  padding: 0;
  position: relative;
  width: 100%;
  z-index: 1;
}

.input__div .border {
  background: #42b983;
  transition: all 0.18s;
  bottom: -1px;
  height: 2px;
  left: 30%;
  opacity: 0;
  pointer-events: none;
  position: absolute;
  right: 30%;
}

.input__div:focus-within .border {
  left: 0;
  right: 0;
  opacity: 1;
}

.material-checkbox {
  position: relative;
  display: inline-block;
  color: rgba(0, 0, 0, 0.87);
  cursor: pointer;
  font-size: 14px;
  line-height: 18px;
}

.material-checkbox > input {
  appearance: none;
  -moz-appearance: none;
  -webkit-appearance: none;
  position: absolute;
  z-index: -1;
  left: -5px;
  top: -5px;
  display: block;
  margin: 0;
  border-radius: 50%;
  width: 18px;
  height: 18px;
  background-color: rgba(0, 0, 0, 0.42);
  outline: none;
  opacity: 0;
  transform: scale(1);
  -ms-transform: scale(0); /* Graceful degradation for IE */
  transition: opacity 0.5s, transform 0.5s;
}

.material-checkbox > input:checked {
  background-color: #2196f3;
}

.material-checkbox > input:disabled {
  opacity: 0;
}

.material-checkbox > input:disabled + span {
  cursor: initial;
}

.material-checkbox > span::before {
  content: "";
  display: inline-block;
  margin-right: 15px;
  border: solid 2px rgba(0, 0, 0, 0.42);
  border-radius: 2px;
  width: 14px;
  height: 14px;
  vertical-align: -4px;
  transition: border-color 0.5s, background-color 0.5s;
}

.material-checkbox > input:checked + span::before {
  border-color: #41b883;
  background-color: #41b883;
}

.material-checkbox > input:active + span::before {
  border-color: #41b883;
}

.material-checkbox > input:checked:active + span::before {
  border-color: transparent;
  background-color: rgba(0, 0, 0, 0.42);
}

.material-checkbox > span::after {
  content: "";
  display: inline-block;
  position: absolute;
  top: 0;
  left: 0;
  width: 5px;
  height: 10px;
  border: solid 2px transparent;
  border-left: none;
  border-top: none;
  transform: translate(5.5px, 1px) rotate(45deg);
  -ms-transform: translate(5.5px, 2px) rotate(45deg);
}

.material-checkbox > input:checked + span::after {
  border-color: #fff;
}
</style>

