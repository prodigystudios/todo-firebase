<template>
  <div class="wrapper">
    <div class="main-container">
      <h2>Att göra</h2>
      <div v-for="todo in todoList" :key="todo.id">
        <div v-if="todo.isCompleted" class="wrapper-inside">
          <div @click="setCompleted(todo.id)" class="todo-completed">
            <p>{{todo.title}}</p>
            <p>{{todo.date}}</p>
          </div>
          <button class="delete-btn" @click="deleteTodo(todo.id)"><img src="../assets/crossmark-transparent.png"
              width="32" height="32"></button>
        </div>
        <div v-else class="wrapper-inside">
          <div @click="setCompleted(todo.id)" class="todo">
            <p>{{todo.title}}</p>
            <p>{{todo.date}}</p>
          </div>
            <button class="delete-btn" @click="deleteTodo(todo.id)"><img src="../assets/crossmark-transparent.png"
              width="32" height="32"></button>
        </div>
      </div>
    </div>
  </div>

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
      tempTodoObj.date = doc.data().date;
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
.wrapper
{
  display: flex;
  justify-content: center;
}

.main-container
{
  width: 700px;
  height: 600px;
  background: #0f1835;
  padding: 20px;
  margin: 30px 0px;
  border-radius: 20px;
}

.wrapper-inside
{
  display: flex;
  gap: 10px;
  align-items: center;
}

.todo
{
  width: 90%;
  text-align: left;
  border: 1px solid gray;
  border-radius: 10px;
  padding: 0px 10px;
  display: flex;
  justify-content: space-between;
  margin: 10px 0px;
}

.todo:hover
{
  cursor: pointer;
}

.todo-completed
{
  width: 90%;
  text-align: left;
  border: 1px solid green;
  border-radius: 10px;
  padding: 0px 10px;
  display: flex;
  justify-content: space-between;
  margin: 10px 0px;
  text-decoration: line-through;
  text-decoration-thickness: 2px;
}

.todo-completed:hover
{
  cursor: pointer;
}


.delete-btn
{
  background: none;
  border: none;
  font-size: 20px;
  font-weight: 900;
  transition: .5s ease;
  filter:grayscale(100);
}

.delete-btn:hover
{
  cursor: pointer;
  filter:grayscale(0);
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
  filter: drop-shadow(0px 0px 10px #4444d1) brightness(110%)
}

.checkmark-grayscale
{
  width: 30px;
  height: 30px;
  filter: grayscale(100);
}

.checkmark-grayscale-completed
{
  width: 30px;
  height: 30px;
  filter: grayscale(0);
}


@media screen and (max-width: 425px)
{
  h2
  {
    margin: 0;
    padding-top: 8px;
    font-size: 15px;
  }

  .wrapper
  {
    width: 100%;
    justify-content: center;
  }

  .container
  {
    width: 100%;
    gap: 40px;
  }

  .container li
  {
    font-size: 12px;
    align-items: center;
  }
}
</style>