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
    <div v-for="(item, index) in items" :key="item.id" class="todo-item">
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
    <div class="additional-section">
      <div>
        <label><input type="checkbox" :checked="!anyRemaining" @change="checkAll">全ての項目をチェックする</label>
      </div>
      <div class="status-bar">
        <button :class="{active:filter ='all'}" @click="filter ='all'">All</button>
        <button :class="{active:filter ='doing'}" @click="filter ='doing'">Doing</button>
        <button :class="{active:filter ='done'}" @click="filter ='done'">Done</button>
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
      idForTodo: 1,
      items: [],
      beforeEditCache: "",
      filter: 'all',
    };
  },
  computed:{
    remaining(){
      return this.items.filter(item => !item.completed).length;
    },
    anyRemaining(){
      return this.remaining !== 0;
    },
  },
  methods: {
    doAdd() {
      if(this.newtodo.trim().length == 0) {
        return
      }
      this.items.push({
        title: this.newtodo,
        id: this.idForTodo,
        beforeEditCache: this.beforeEditCache,
        completed: false,
        editing: false,
      }),
      this.newtodo = "";
      this.idForTodo += 1;
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
    checkAll(){
      this.items.forEach((item) => item.completed = event.target.completed);
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
<style>
@import url(https://fonts.googleapis.com/css?family=Open+Sans:400|Raleway:300);

.header h1{
  width: 20%;
  padding: 5px 20px;
  border-radius: 20px;
  background: aquamarine;
  margin: 0 auto;
  margin-bottom: 40px;
}
.todomaker{
  width: 40%;
  margin: 0 auto;
  display: flex;
}
.todomaker input {
  width: 80%;
  padding: 10px;
  text-align: center;
  font-size: 20px;
  margin-right: 25px;
  margin-bottom: 40px;
}
.todomaker button{
  width: 75px;
  height: 45px;
  padding: 10px;
  font-size: 20px;
  -webkit-transition: all 200ms cubic-bezier(0.390, 0.500, 0.150, 1.360);
    -moz-transition: all 200ms cubic-bezier(0.390, 0.500, 0.150, 1.360);
    -ms-transition: all 200ms cubic-bezier(0.390, 0.500, 0.150, 1.360);
    -o-transition: all 200ms cubic-bezier(0.390, 0.500, 0.150, 1.360);
    transition: all 200ms cubic-bezier(0.390, 0.500, 0.150, 1.360);
}
.todomaker button:hover{
  color: rgba(255, 255, 255, 0.85);
  box-shadow: rgba(30, 22, 54, 0.7) 0 0px 0px 40px inset;
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
.additional-section{
  display: flex;
  justify-content: space-between;
  width: 40%;
  margin: 0 auto;
  margin-top: 30px;
  padding: 10px 0;
  border-top: 1px solid gray;
}
.status-bar button{
  margin-right: 10px;
  background-color: white;
  appearance: none;
}
.status-bar button:hover{
  background: aquamarine;
}
.status-bar button:focus{
  outline: none;
}
.active{
  background-color: aquamarine;
}
@media screen and (max-width: 480px){
  .header h1{
    width: 40%;
    padding: 5px 20px;
    border-radius: 20px;
    background: aquamarine;
    margin: 0 auto;
    margin-top: 5vh;
    margin-bottom: 40px;
  }
  .todomaker{
    width: 80%;
    margin: 0 auto;
    display: flex;
  }
  .todomaker input {
    width: 60%;
    padding: 10px;
    text-align: center;
    font-size: 12px;
    margin-right: 25px;
    margin-bottom: 40px;
  }
  .todomaker button{
    width: 60px;
    height: 35px;
    padding: 5px;
    font-size: 20px;
    -webkit-transition: all 200ms cubic-bezier(0.390, 0.500, 0.150, 1.360);
    -moz-transition: all 200ms cubic-bezier(0.390, 0.500, 0.150, 1.360);
    -ms-transition: all 200ms cubic-bezier(0.390, 0.500, 0.150, 1.360);
    -o-transition: all 200ms cubic-bezier(0.390, 0.500, 0.150, 1.360);
    transition: all 200ms cubic-bezier(0.390, 0.500, 0.150, 1.360);
  }
  .todomaker button:hover{
    color: rgba(255, 255, 255, 0.85);
    box-shadow: rgba(30, 22, 54, 0.7) 0 0px 0px 40px inset;
  }
  .todo-item{
    width: 70%;
  }
  .additional-section{
    width: 70%;
  }
  .additional-section label{
    font-size: 12px;
  }
  .additional-section span{
    font-size: 14px;
  }
  .status-bar{
    display: none;
  }
}
</style>
