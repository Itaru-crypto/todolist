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
        @keyup.enter="doAdd(newtodo)"
      />
      <button @click="doAdd(newtodo)">Add</button>
    </div>
    <div v-for="(item, index) in items" :key="index" class="todo-item">
      <div class="todo-item-left">
        <div v-if="!item.editing" @dblclick="editTodo(item)" class="todo-item-label" :class="{completed: item.completed}">
          <input type="checkbox" v-model="item.completed"/>
          {{ item.title }}
        </div>
        <input v-else class="edit-active" type="text" v-model="item.title" @blur="endEdit(item)" @keyup.enter="endEdit(item)" @keyup.esc="cancelEdit(item)">
      </div>
      <div class="delete-item" @click="deleteTodo(index)">
        &times; 
      </div>
    </div> 
    <div class="status-bar">
      <div>
        <label><input type="checkbox" v-model="allcompleted">全ての項目をチェックする</label>
      </div>
      <span>残り {{remaining}} 項目</span>
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
      beforeEditCache: "",
      editing: false
    };
  },
  computed:{
    remaining(){
      return this.items.filter(item => !item.completed).length
    }
  },
  methods: {
    doAdd() {
      if(this.newtodo.trim().length == 0) {
        return
      }
      this.items.push({
        title: this.newtodo,
        beforeEditCache: this.beforeEditCache,
        completed: this.completed,
        editing: this.editing,
      }),
      this.newtodo = "";
      this.saveTodo();
    },
    editTodo(item){
      this.beforeEditCache = item.title;
      item.editing = true;
    },
    endEdit(item){
      if (item.title.trim().length == 0){
        item.title = this.beforeEditCache
      }
      item.editing = false;
    },
    cancelEdit(item){
      item.title = this.beforeEditCache;
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
.header h1{
  background: aquamarine;
}
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
.todo-item-left{
  display: flex;
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
.status-bar{
  display: flex;
  justify-content: space-between;
  width: 40%;
  margin: 0 auto;
  margin-top: 30px;
  padding: 10px 0;
  border-top: 1px solid gray;
}
</style>
