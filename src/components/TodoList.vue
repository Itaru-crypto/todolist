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
      <div>
        <label v-bind:class="{ completed: item.completed}">
          <input type="checkbox" v-model="item.completed"/>{{ item.title }}
        </label>
      </div>
      <div class="remove-item">
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
      idForTodo: 1,
      newtodo: "",
      items: [],
      completed: false
    };
  },
  methods: {
    doAdd() {
      if(this.newtodo.trim().length == 0) {
        return
      }
      this.items.push({
        id: this.idForTodo,
        title: this.newtodo,
        completed: this.completed,
      }),
      this.newtodo = "";
      this.idForTodo ++;
      this.saveToDo();
    },
    deleteToDo(index) {
      this.items.splice(index,1);
      this.saveToDo();
    },
    saveToDo() {
      localStorage.setItem('items', JSON.stringify(this.items));
    },
    loadToDO() {
      this.items = JSON.parse(localStorage.getItem('items'));
      if (!this.items){
        this.items = []
      }
    }
  },
  mounted: function(){
    this.loadToDO();
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
  margin-bottom: 12px;
  display: flex;
  text-align: left;
  justify-content: space-between;
  width: 60%;
}
.completed{
  text-decoration: line-through;
}
</style>
