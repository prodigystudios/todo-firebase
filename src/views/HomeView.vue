<template>
  <div class="heya">
    <input v-on:keydown.enter="saveTodo()" class="input-field" type="text"
      placeholder="Vad behöver du göra?" v-model="title" />
    <div>
      <get-todos :key="todoKey" />
    </div>
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
    saveTodo() {

      const docRef = addDoc(collection(GetDB(), "Todo"),
        {
          title: this.title,
          isCompleted: false,
        });
      this.todoKey++;
      this.title = '';
    }
  }
}
</script>
<style scoped>
.heya
{
  height: 1000px;
}

.input-field
{
  text-align: center;
  background: #27274b;
  color:whitesmoke;
  filter:drop-shadow(0px 0px 5px #4444d1) brightness(110%);
  width:80%;
  height:30px;
  border-radius: 4px;
}
.input-field::placeholder {
  color:white;
}

.input-field:focus
{
  outline: none;
}
</style>
