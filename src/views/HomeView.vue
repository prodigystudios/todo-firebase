<template>
    <input v-on:keydown.enter="saveTodo()" class="input-field" required title="Must be atleast 3 characters long" type="text" placeholder="Vad behöver du göra?"
      v-model="title" />
    <div>
      <get-todos :key="todoKey" />
    </div>
</template>

<script>
import GetTodos from '@/components/GetTodos.vue'

import { addDoc, collection } from "firebase/firestore";
import { GetDB } from "@/firebase";

export default {
  name: 'test',
  components: {
    GetTodos,
  },


  data() {
    return {
      title: '',
      todoKey: 0,
      todoList: [],
    }
  },

  methods: {

    GetDate() {
      const date = new Date().getDate();
      const month = new Date().getMonth() + 1;
      const year = new Date().getFullYear();
      console.log(month);
      return date + "/" + month + "/" + year;
    },
    saveTodo() {
      if (this.title.length > 3) {
        const docRef = addDoc(collection(GetDB(), "Todo"),
          {
            title: this.title,
            isCompleted: false,
            date: this.GetDate(),
          });
        this.todoKey++;
        this.title = '';
      }
    }
  },
  
}
</script>
<style scoped>

.input-field
{
  text-align: center;
  background: #27274b;
  color: whitesmoke;
  filter: drop-shadow(0px 0px 5px #4444d1) brightness(110%);
  width: 38%;
  height: 30px;
  border-radius: 10px;
  margin-top:50px;
}

.input-field::placeholder
{
  color: white;
}

.input-field:focus
{
  outline: none;
}
</style>
