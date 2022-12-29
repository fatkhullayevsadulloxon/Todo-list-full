<script setup>
import { ref, onMounted, computed, watch } from "vue";

const todos = ref([]);
const name = ref("");

const input_content = ref("");
const input_category = ref(null);

const todos_asc = computed(() =>
  todos.value.sort((a, b) => {
    return b.createdAt - a.createdAt;
  })
);

const addTodo = () => {
  if (input_content.value.trim() === "" || input_category.value === null) {
    return;
  }

  todos.value.push({
    content: input_content.value,
    category: input_category.value,
    done: false,
    createdAt: new Date().getTime()
  });

  input_content.value = "";
  input_category.value = null;
};

const removeTodo = (todo) => {
    todos.value = todos.value.filter(t => t !== todo)
}

watch(
  todos,
  newVal => {
    localStorage.setItem("todos", JSON.stringify(newVal));
  },
  { deep: true }
);

watch(name, newVal => {
  localStorage.setItem("name", newVal);
});

onMounted(() => {
  name.value = localStorage.getItem("name") || "";
  todos.value = JSON.parse(localStorage.getItem("todos")) || [];
});
</script>

<template>
    <main class="app">
      <section class="greeting">
          <h2 class="title">
              Nima gap, <input type="text" placeholder="ismingizni kiriting" v-model="name"/>
          </h2>
      </section>

      <section class="create-todo">
        <h3>TOPSHIRIQ YARATISH</h3>
      
        <form @submit.prevent="addTodo">
          <h4>Vazifalar ro'yxatiga nimalar kiritmoqchisiz?</h4>
          <input 
            type="text" 
            placeholder="masalan: texnomartdan noutbuk sotib olish" 
            v-model="input_content" />

          <h4>Categoriyani tanlang</h4>
          <div class="options">

            <label>
              <input 
               type="radio"
               name="category" 
               value="business" 
               v-model="input_category"/>
              <span class="bubble business"></span>
              <div>Biznes</div>
            </label>
            
            <label>
              <input 
               type="radio"
               name="category" 
               value="personal" 
               v-model="input_category"/>
              <span class="bubble personal"></span>
              <div>Personal</div>
            </label>

            <input type="submit" value="Add Todo">
          </div>
        </form>
      </section>

      <section class="todo-list">
        <h3>TODO LIST</h3>
        <div class="list">

              <div v-for="todo in todos_asc" :class="`todo-item ${todo.done && 'done'}`">

                  <label>
                      <input type="checkbox" v-model="todo.done"/>
                      <span :class="`bubble ${todo.category}`"></span>
                  </label>

                  <div class="todo-content">
                      <input type="text" v-model="todo.content">
                  </div>

                  <div class="actions">
                      <button class="delete" @click="removeTodo(todo)">Delete</button>
                  </div>

              </div>

        </div>
      </section>
    </main>
</template>
