<template>
   <div>
    <h1>Todos</h1>
     <input
      type="text"
      v-model="todoName"
      @keyup.enter="addTodo"
      aria-label="Add a new Todo"
      placeholder="Add a new Todo"
    />
    <ul>
      <li v-for="todo of todos"
         :key="todo.id" 
         :class="{ done: todo.done }"
          @click="doneTodo(todo)"
          @dblclick="deleteTodo(todo.id)"
         >
        {{ todo.name }}
      </li>
     
    </ul>
  </div>

</template>

<script>
import axios from 'axios'
const baseURL = "http://localhost:3001/todos";
export default {
 name:'Todos',
 data () {
    return {
        todos: [],
        todoName:''
    }
 },
  created() {
 /*try {
      const res = await axios.get(baseURL);
      this.todos = res.data;
    } catch (e) {
      console.error(e);
    }*/
  this.getTodos()

 },
 methods: {
 async getTodos(){
  try {
      const res = await axios.get(baseURL);
      this.todos = res.data;
    } catch (e) {
      console.error(e);
    }
 },
 async addTodo() {
      try {
        const res = await axios.post(baseURL, { name: this.todoName, done:false });
        //this.todos = [...this.todos, res.data];
        if(res.status === 201) {
         this.todos = [...this.todos, res.data];
        //this.getTodos()
        }
        this.todoName = "";
      } catch (e) {
        console.error(e);
      }
    },
async doneTodo(todo) {

      try {
       const res = await axios.patch(`${baseURL}/${todo.id}`, {
          done: !todo.done
        });
        if(res.status === 200) {
        this.todos = this.todos.map(t => {
          if (t.id === todo.id) {
            t.done = !t.done
          }

          return t
        })
        }
      } catch (e) {
        console.error(e);
      }
    },
 async deleteTodo(id) {
    try {
       const res = await axios.delete(`${baseURL}/${id}`);
        if(res.status === 200) {
        this.todos = this.todos.filter(todo => todo.id !== id)
        }
      } catch (e) {
        console.error(e);
      }
 }

 }


}
</script>

<style scoped>
h1 {
  text-decoration: underline;
}

li {
  color: white;
  margin-right: 10px;
}
input {
  width: 100%;
  padding: 1rem;
  border-radius: 0.4rem;
  border: 1px solid #fd9644;
  margin-bottom: 2rem;
  font-size: 1.5rem;
}


.done {
  text-decoration: line-through;
}

</style>
