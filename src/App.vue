<template>
  <div class="whole">
    <div class="d-flex header-input">
      <div class="d-flex">
        <a href="#" @click="checkAllDone()" v-if="checkAll == true"
          ><svg width="24" height="24" xmlns="http://www.w3.org/2000/svg" fill-rule="evenodd" clip-rule="evenodd"><path d="M23.245 4l-11.245 14.374-11.219-14.374-.781.619 12 15.381 12-15.391-.755-.609z"/></svg></a>
        <a href="#" @click="uncheckAllDone()" v-if="checkAll == false"
          ><svg width="24" height="24" xmlns="http://www.w3.org/2000/svg" fill-rule="evenodd" clip-rule="evenodd"><path d="M23.245 20l-11.245-14.374-11.219 14.374-.781-.619 12-15.381 12 15.391-.755.609z"/></svg></a>
      </div>
      <form @submit.prevent="addTodo">
        <input id="message" v-model.trim="newTodo" placeholder="Add a todo" />
      </form>
    </div>
    <div id="line"></div>

    <section class="lists-group">
      <div class="list-item" v-for="(todo, index) in todos" :key="index">
        <input
          id="edit"
          type="text"
          v-if="todo.edit"
          v-model="todo.content"
          @keyup.esc="esTodo(todo)"
          @keyup.enter="updateTodo(todo,todo.content),todo.edit = false"
          @blur="updateTodo(todo,todo.content),todo.edit = false"
        />
        <div class="content-whole" v-else>
          <div class="content">
            <input
              type="checkbox"
              @click="toggleDone(todo)"
              v-model="todo.confirm"
            />
            <span :class="{ done: todo.confirm }" @dblclick="todo.edit = true"     @click="toggleDone(todo)">
              {{ todo.content }}</span
            >
          </div>
          <a href="" @click="removeTodo(index)"
            ><i class="fa-solid fa-x"></i
          ></a>
        </div>
      </div>
    </section>

    <div class="">
      <div class="footer">
        <p>
          <span class="totalItem">{{ countItemLeft() }}  </span>: item left
        </p>
        <div class="d-flex">
          <div class="mid-a">
            <a href="#" @click="allTodo()" id="all">all</a>
            <a href="#" @click="unCheck()">active</a>
            <a href="#" @click="allCheck()">completed</a>
          </div>
        </div>
        <a href="#" @click="removeAllDone()" class="allclear"
          >clear completed</a
        >
      </div>
    </div>
  </div>
</template>

<script>
import { ref, onMounted, watch,computed } from "vue";

export default {
  setup() {
    const todos = ref([]);
    const checkAll = ref(true);
    const newTodo = ref("");
    const itemLeft=ref([]);
    
       

    const addTodo = () => {
   if(newTodo.value.length==0){
    return
   }
     todos.value.push({
        id: new Date().getMilliseconds(),
        content: newTodo.value,
        confirm: false,
        edit: false,
      });
      addStorage(todos.value);
      newTodo.value = "";
    };

    const removeTodo = (index) => {
      todos.value.splice(index, 1);
      addStorage(todos.value);
    };

    function toggleDone(todo) {
      todo.confirm = !todo.confirm;
      addStorage(todos.value);
    }

    function checkAllDone() {
      todos.value.forEach((todo) => (todo.confirm = true));
      checkAll.value = false;
      addStorage(todos.value);
    }

    function uncheckAllDone() {
      todos.value.forEach((todo) => (todo.confirm = false));
      checkAll.value = true;
      addStorage(todos.value);
    }

    function removeAllDone() {
      todos.value = todos.value.filter((el) => el.confirm !== true);
      addStorage(todos.value);
    }

    //update()
    function updateTodo(todo, y) {
      todo.content = y;
      todo.edit = false;
      addStorage(todos.value);
    }
    function esTodo(todo){
     todos.value = 
        JSON.parse(localStorage.getItem("todos")) || [];
        addStorage(todos.value);
    }
    function addStorage(newVal) {
      localStorage.setItem("todos", JSON.stringify(newVal));
    }

    onMounted(() => {
      todos.value = JSON.parse(localStorage.getItem("todos")) || [];
      // countItemLeft();
    });

    function allCheck() {
      todos.value = 
        JSON.parse(localStorage.getItem("todos")) || [];
      todos.value = todos.value.filter((el) => el.confirm !== false);
    }

    function unCheck() {
      todos.value =  JSON.parse(localStorage.getItem("todos")) || [];
      todos.value = todos.value.filter((el) => el.confirm !== true);
      return todos.value;
    }

    //allTodo()
    function allTodo() {
     todos.value =
        JSON.parse(localStorage.getItem("todos")) || [];
      todos.value = todos.value;
    }
        
    // const countItemLeft = computed(() => {
    //   itemLeft.value = JSON.parse(localStorage.getItem("todos")) || [];
    //     itemLeft.value = itemLeft.value.filter((el) => el.confirm !== true);
    //     return itemLeft.value.length;
    // })
    
    function countItemLeft(){
          itemLeft.value = JSON.parse(localStorage.getItem("todos")) || [];
        itemLeft.value = itemLeft.value.filter((el) => el.confirm !== true);
        return itemLeft.value.length;
    }

    return {
      todos,
      newTodo,
      addTodo,
      checkAll,
      removeAllDone,
      allCheck,
      unCheck,
      updateTodo,
      addStorage,
      toggleDone,
      checkAllDone,
      itemLeft,
      countItemLeft,
      esTodo,
      uncheckAllDone,
      removeTodo,
      allTodo,
    };
  },
};
</script>
<style scoped>
input[type="checkbox"] {
  transform: scale(1.5);
}
.done {
  text-decoration: line-through;
}
.d-flex {
  display: flex;
  align-items: center;
}
.mr-3 {
  margin-right: 20px;
}
.whole {
  background-color: #fff;
  padding: 10px;
  width: 700px;
  margin-left: 200px;
  border-radius: 10px;
  box-shadow: rgba(50, 50, 93, 0.25) 0px 30px 60px -12px,
    rgba(0, 0, 0, 0.3) 0px 18px 36px -18px;
}
#line {
  padding: 1px;
  background: #b5b5b5;
  margin: 10px 0;
  border-radius: 10px;
  width: 100%;
}

#message {
  border: none;
  outline: none;
  width: 100%;
  font-size: 25px;
  height: 59px;
  padding-left: 30px;
  background-color: transparent;
}
#display-none {
  display: none;
}
.display-none {
  display: none;
}

a {
  text-decoration: none;
  color: black;
}
#edit {
  padding: 5px;
  font-size: 20px;
  width: 100%;
  outline: none;
}
.content-whole {
  display: flex;
  align-items: center;
  padding: 10px 0;
  justify-content: space-between;
}

.content-whole a {
  margin-right: 10px;
}
.content {
  display: flex;
  align-items: center;
}
.content input {
  margin-right: 15px;
}
.content span {
  font-size: 25px;
  width: 400px;
}
::placeholder {
  font-size: 25px;
}

.lists-group {
  width: 100%;
  margin: 5px 0;
  transition: 0.5s;
  align-items: center;
  position: relative;
}
.list-item {
  border-bottom: 1px solid #dddddd;
  padding: 0 20px;
}
.lists-group p i {
  font-size: 25px;
  margin-right: 64px;
  margin-left: 20px;
}

.lists-group #input {
  font-size: 26px;
}
.lists-group .editinput {
  padding: 10px;
}
.lists-group .crop {
  position: absolute;
  right: 40px;
}
.footer {
  padding: 0px 0;
  display: flex;
  align-items: center;
  justify-content: space-between;
}
.footer p {
  font-size: 22px;
  display: flex;
  color: #7d7e80;
}
.mid-a {
  font-size: 22px;
  width: 0px;
  display: flex;
  align-content: center;
}
#all {
  display: inline-block;
  padding: 3px 5px;
  border: 1px solid rgb(170, 170, 170);
}
.mid-a a {
  color: #7d7e80;
  margin-right: 12px;
}
.allclear {
  margin-left: 170px;
  color: #7d7e80;
  font-size: 22px;
}
</style>
