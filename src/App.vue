<template>
  <div id="app">
    <div class="container">
      <TodoInput @add="create"/>

      <div class="card text-center">
        <!-- Tabs -->
        <div class="card-header">
          <ul class="nav nav-tabs card-header-tabs">
            <li class="nav-item">
              <a class="nav-link" href="#" 
              @click.prevent="tab = null"
              :class="{active : tab == null}">全部</a>
            </li>
            <li class="nav-item">
              <a class="nav-link" href="#"
              @click.prevent="tab = false"
              :class="{active : tab == false}">進行中</a>
            </li>
            <li class="nav-item">
              <a class="nav-link" href="#"
              @click.prevent="tab = true"
              :class="{active : tab == true}"
              >已完成</a>
            </li>
          </ul>
        </div>

        <!-- List -->
        <ul class="list-group list-group-flush text-left">
          <li class="list-group-item" v-for="(todo,i) in todoFilter" :key="i" @dblclick="edit(todo)">
              <div  class="d-flex" v-if="todo.id !== catchTodo.id">
                <div class="form-check" >
                <input type="checkbox" class="form-check-input" :id="i"
                v-model="todo.status" @click="checkTodo(todo.id)"
                >
                </div>
                <label class="form-check-label" :for="i" :class="{'text-decoration-line-through':false}">
                  {{todo.title}}
                </label>
                <button type="button" class="btn-close ms-auto" @click="remove(todo.id)"></button>
              </div>
    
              <div class="input-group" v-else>
                <input type="text" class="form-control"
                placeholder="Edit Your Todo ..." 
                @keyup.enter="update(todo.id)"
                @keyup.esc="cancel"
                v-model="catchTitle">
                <button class="btn btn-outline-secondary" type="button" @click.prevent="update(todo)">Update</button>    
              </div>
          </li>
        </ul>

        <!-- Footer -->
        <div class="card-footer d-flex justify-content-between">
          <span>還有 {{ notCompleted.length }} 筆任務未完成</span>
          <a href="#" class="text-decoration-none" @click="removeAll">清除所有任務</a>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import TodoInput from './components/TodoInput.vue'
export default {
  name: 'App',
  components: {
    TodoInput
  },
  data(){
    return {
      list : [],
      catchTodo : {},
      catchTitle : '',
      tab:null,
    }
  },  
  watch:{
    list : {
      handler(value){
        console.log(value)
        localStorage.setItem('list',JSON.stringify(value))
      },
      deep:true
    },
  },
  computed:{
    notCompleted(){
      return this.list.filter((todo)=> !todo.status )
    },
    todoFilter(){
      switch(this.tab){
        case true :
          return this.list.filter((todo)=> todo.status )
        case false :
          return this.list.filter((todo)=> !todo.status)
        default :
          return this.list
      }
    }
  },
  methods:{
    create(todo){
      const obj = { id : Math.floor(Date.now() / 1000), title : todo, status : false}
      this.list.push(obj);
    },
    edit(todo){
      this.catchTodo = todo;
      this.catchTitle = todo.title
    },
    update(id){
      this.list.forEach((todo)=>{
        if(todo.id == id){
          todo.title = this.catchTitle;
        }
      })

      this.cancel()
    },
    cancel(){
      this.catchTitle = '';
      this.catchTodo = {}
    },
    remove(id){
      this.list = this.list.filter((todo)=> todo.id != id)
    },
    removeAll(){
      this.list = []
    },
    checkTodo(id){
      this.list.forEach((todo)=>{
        if(todo.id == id){ todo.status = !todo.status }
      }) 
    },
  },
  created(){
    localStorage.getItem('list') == null?
    this.list = [] : this.list = JSON.parse(localStorage.getItem('list'))
  }
}
</script>
<style lang="scss">
  body{
    margin : 50px auto 50px auto
  }
</style>