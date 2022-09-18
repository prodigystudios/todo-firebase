<template>
  <ol>
    <section class="wrapper" v-for="todo in todoList" :key="todo.id">
      <div v-if="!todo.isCompleted" class="container">
        <div class="clickable-container" @click="setCompleted(todo.id)">
          <li>
            {{todo.title}}
          </li>
        </div>
        <img class="checkmark-grayscale" src="../assets/checkmark-png-25977.png" width="30" height="30">
        <button class="delete-btn" @click="deleteTodo(todo.id)">X</button>
      </div>
      <div v-else class="container">
        <div class="clickable-container" @click="setCompleted(todo.id)">
          <li>
            {{todo.title}}
          </li>
        </div>
          <img class="checkmark-grayscale-completed" src="../assets/checkmark-png-25977.png" width="30" height="30">
          <button class="delete-btn" @click="deleteTodo(todo.id)">X</button>
        </div>
    </section>
  </ol>
  <button @click="deleteAll()" class="deleteAll-btn">Ta bort alla poster</button>
</template>

<script>
import { doc, updateDoc, deleteDoc, collection, getDocs } from "firebase/firestore";
import { GetDB } from '@/firebase';
export default {
  name: "GetTodos",
  data() {
    return {
      todoList: [],
    }
  },
  async created() {
    const querySnapshot = await getDocs(collection(GetDB(), "Todo"));
    querySnapshot.forEach((doc) => {
      const tempTodoObj = {}
      tempTodoObj.id = doc.id;
      tempTodoObj.title = doc.data().title;
      tempTodoObj.isCompleted = doc.data().isCompleted;
      this.todoList.push(tempTodoObj);
    });
    console.table(this.todoList);
  },

  methods: {
    deleteTodo(id) {
      const singelPost = this.todoList.find((todo) => todo.id == id);
      const index = this.todoList.indexOf(singelPost);
      deleteDoc(doc(GetDB(), "Todo", singelPost.id));
      this.todoList.splice(index, index + 1);
    },
    deleteAll() {
      this.todoList.forEach(todo => {
        deleteDoc(doc(GetDB(), "Todo", todo.id));
        this.todoList = [];
      })
    },

    async setCompleted(id) {
      const singelPost = this.todoList.find((todo) => todo.id == id);
      if (singelPost.isCompleted == true) {
        singelPost.isCompleted = false;
      } else {
        singelPost.isCompleted = true;
      }
      //save changes to firebase
      const todoRef = doc(GetDB(), "Todo", id);
      await updateDoc(todoRef, {
        isCompleted: singelPost.isCompleted,
      });
    }
  },
}

</script>

<style scoped>
ol
{
  width: 100%;
  color: white;
  padding: 0;
}

.wrapper
{
  display: flex;
  justify-content: center;
}

.container
{
  display: flex;
  justify-content: center;
  margin: 20px 0px;
  width: 80%;
  border: 1px solid black;
  border-radius: 100px;
  filter:drop-shadow(0px 0px 5px #4444d1) brightness(110%);
  box-shadow: 1px 1px 50px 20px #0f1835 inset;
  padding: 20px;
}

.container li
{
  text-align: left;
  width: 250px;
  font-size: 20px;
  font-weight: 900;
}

.clickable-container
{
  width: 50%;
}

.clickable-container:hover
{
  cursor: pointer;
}

.delete-btn
{
  background: none;
  border: none;
  font-size: 20px;
  font-weight: 900;
  color: gray;
  transition: .5s ease;
}

.delete-btn:hover
{
  color: red;
}

.deleteAll-btn
{
  background: #23346d;
  color: white;
  border: none;
  cursor: pointer;
  border-radius: 10px;
  width: 300px;
  padding: 20px;
  font-size: 18px;
  box-shadow: 3px 4px black;
  transition: .2s ease;
}

.deleteAll-btn:hover
{
  filter:drop-shadow(0px 0px 10px #4444d1) brightness(110%)
}

.checkmark-grayscale
{
  width: 30px;
  height: 30px;
  position: absolute;
  left: 950px;
  filter: grayscale(100);
}

.checkmark-grayscale-completed
{
  width: 30px;
  height: 30px;
  position: absolute;
  left: 950px;
  filter: grayscale(0);
}

::marker
{
  font-size: 30px;
}

</style>