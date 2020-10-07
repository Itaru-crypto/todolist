<template>
  <div>
    <div class="header">
      <h1>To do List</h1>
    </div>
    <div class="todomaker">
      <input
        placeholder="予定を入力してください"
        type="text"
        v-model="newtodo"
      />
      <button @click="doAdd(newtodo)">Add</button>
    </div>
    <div v-for="(item, index) in items" :key="index" class="todo-item">
      <div class="todo-item-left">
        <!--<label v-bind:class="{completed: item.completed}">-->
        <div v-if="!item.editing" @dblclick="editTodo(item)" class="todo-item-label">
          <!--<input type="checkbox" v-model="item.completed"/>-->
          {{ item.title }}
        </div>
        <input v-else class="edit-active" type="text" v-model="item.title" @blur="endEdit(item)" @keyup.enter="endEdit(item)">
        <!--</label>-->
      </div>
      <div class="delete-item" @click="deleteTodo(index)">
        &times; 
      </div>
    </div>    
  </div>
</template>
<script>
export default {
  name: 'todolist',
  data() {
    return {
      newtodo: "",
      items: [],
      completed: false,
      editing: false
    };
  },
  methods: {
    doAdd() {
      if(this.newtodo.trim().length == 0) {
        return
      }
      this.items.push({
        title: this.newtodo,
        completed: this.completed,
        editing: this.editing,
      }),
      this.newtodo = "";
      this.saveTodo();
    },
    editTodo(item){
      item.editing = true;
    },
    endEdit(item){
      item.editing = false;
    },
    deleteTodo(index) {
      this.items.splice(index,1);
      this.saveTodo();
    },
    saveTodo() {
      localStorage.setItem('items', JSON.stringify(this.items));
    },
    loadTodo() {
      this.items = JSON.parse(localStorage.getItem('items'));
      if (!this.items){
        this.items = []
      }
    }
  },
  mounted: function(){
    this.loadTodo();
  },
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.todomaker input {
  width: 40%;
  padding: 10px;
  text-align: center;
  font-size: 20px;
  margin-right: 25px;
  margin-bottom: 40px;
}
.todomaker button{
  width: 50px;
  padding: 10px;
}
li {
  list-style: none;
}
.todo-item{
  width: 40%;
  align-items: center;
  display: flex;
  font-size: 20px;
  margin: 0 auto;
  margin-bottom: 12px;  
  justify-content: space-between;
}
.remove-item{
  cursor: pointer;
}
.completed{
  text-decoration: line-through;
}
.edit-active{
  font-size: 20px;
  color: #2c3e50;
  margin-left: 12px;
  widows: 100%;
  padding: 10px;
  border-radius: 1px solid #ccc;
}
</style>
