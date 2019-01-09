<template>
  <div id="app">
    <div class="jumbotron jumbotron-fluid bg-image mb-0">
      <div class="container pl-4">
        <h1 class="h4 pt-2">Hi，完成任務了嗎？</h1>
        <p class="lead">{{ today }}</p>
      </div>
    </div>
    <nav class="navbar navbar-light bg-info">
      <ul class="nav nav-pills mr-auto ml-auto">
        <li class="nav-item">
          <a class="nav-link mr-4" :class="{active: currentPage === 'all'}" @click="currentPage = 'all'" href="#">我的任務</a>
        </li>
        <li class="nav-item">
          <a class="nav-link mr-4" :class="{active: currentPage === 'progress'}" @click="currentPage = 'progress'" href="#">進行中</a>
        </li>
        <li class="nav-item">
          <a class="nav-link" :class="{active: currentPage === 'completed'}" @click="currentPage = 'completed'" href="#">已完成</a>
        </li>
      </ul>
    </nav>
    <div class="container">
      <div class="todo-wrap mt-3 d-flex" v-if="isNewTodo === false">
        <div class="h5 text-dark d-flex align-items-center mb-0">有什麼計畫？</div>
        <a href="#" class="btn btn-primary text-white ml-auto" @click.prevent="isNewTodo = true">新增任務</a>
      </div>
      <EditItem v-if="isNewTodo === true"
        :getTodos="todos"
        @closeEditTodo = "closeEdit"
        @getNewData = "updateData"
      ></EditItem>
      <draggable @end="dragItem" v-model="todos" :options="{handle:'.handle'}">
        <div v-for="item in todos" :key="item.id" v-if="currentPage === 'all' || currentPage == item.completed">
          <TodoItem :todo="item"
            @getNewData = "updateData" 
            @editCurrentTodo = "openEdit"
          ></TodoItem>

          <EditItem v-if="currentEdit.id === item.id"
            :todo = "currentEdit"
            @closeEditTodo = "closeEdit"
            @getNewData = "updateData"
          >
          </EditItem>
        </div>
        <!-- <div class="todo-wrap text-center text-dark mt-3 mb-3 border p-4"
          v-if="currentPage === 'completed' && completedTodos.length == 0">
          目前沒有已完成的任務哦！
        </div> -->
      </draggable>
    </div>
  </div>
</template>

<script>
import TodoItem from './components/TodoItem';
import EditItem from './components/EditItem';
import draggable from 'vuedraggable';

export default {
  name: 'App',
  components: {
    TodoItem,
    EditItem,
    draggable,
  },
  data () {
    return {
      isNewTodo: false,
      todos: [],
      sortData: [],
      currentPage: 'all',
      currentEdit: {},
      // completedTodos: [],
    }
  },
  methods: {
    closeEdit () {
      this.isNewTodo = false;
      this.currentEdit = {};
    },
    getData () {
      const vm = this;
      const api = 'http://localhost:5000/todos';
      const sortApi = 'http://localhost:5000/sort';
      let todos = [];
      vm.todos = [];
      vm.$http.get(api).then((response) => {
        console.log(response.data);
        todos = response.data;
        return vm.$http.get(sortApi);
      }).then((response) => {
        console.log(response);
        vm.sortData = response.data.sort;
        if(vm.sortData) {
          vm.sortData.forEach((sortId) => {
            console.log(todos)
            const todo = todos.find((item) => item.id === sortId);
            vm.todos.push(todo);
          });
          todos.forEach((todo) => {
            const hasSort = vm.sortData.some((sortId) => todo.id === sortId)
            if (!hasSort) {
              vm.todos.push(todo);
            }
          })
        } else{
          vm.todos = todos;
        }
        // const completedTodo = vm.todos.find((item) => item.completed === 'completed');
        // vm.completedTodos.push(completedTodo);
        // console.log(vm.completedTodos.length)
      });
    },
    dragItem () {
      const vm = this;
      const sortApi = 'http://localhost:5000/sort';
      const sort = vm.todos.map((item) => {
        return item.id;
      });
      console.log(sort)
      vm.$http.post(sortApi, {sort: sort}).then((response) => {
          console.log(response.data)
      })
    },
    openEdit (id) {
      const vm = this;
      vm.isNewTodo = false;
      vm.currentEdit = vm.todos.find((item) => item.id === id);
    },
    updateData () {
      this.getData();
      this.currentEdit = {};
    }
  },
  computed: {
    today () {
      const today = new Date(); 
      const day_list = ['日', '一', '二', '三', '四', '五', '六'];
      const day = today.getDay();
      return `${today.getFullYear()}年${(today.getMonth()+1)}月${today.getDate()}日 星期${day_list[day]}`;
    },
    completedTodoLen () {
      const vm = this;
      console.log(vm.todos)
    }
  },
  created () {
    this.getData();    
  }
}
</script>

<style lang="scss">
@import "./assets/all";
</style>