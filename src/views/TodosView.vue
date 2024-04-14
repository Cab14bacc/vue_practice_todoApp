<script setup>
import TodoCreator from '@/components/TodoCreator.vue';
import { ref , watch, computed} from "vue";
import { uid } from "uid";
import TodoItem from '@/components/TodoItem.vue';
import { Icon } from "@iconify/vue"
const todoList = ref([]);

watch(todoList, (newVal, oldVal) => {
  setTodoListLocalStorage();
}, {
  deep: true,
});

const todoCompleted = computed(() => { 
    return todoList.value.every((todo)=>{return todo.isCompleted});
});


const setTodoListLocalStorage = () => {
  localStorage.setItem("todoList", JSON.stringify(todoList.value));
};

const fetchTodoList = () => {
  const savedTodoList = JSON.parse(localStorage.getItem("todoList"));
  if (savedTodoList) {
    todoList.value = savedTodoList;
  }
};

fetchTodoList();

const createTodo = (todo) => {
  todoList.value.push({
    id: uid(),
    todo,
    isCompleted: null,
    isEditing: null,
  });
};

const toggleTodoComplete = (todoIndex) => { 
  todoList.value[todoIndex].isCompleted = !todoList.value[todoIndex].isCompleted;
};
const toggleEditTodo = (todoIndex) => {
  todoList.value[todoIndex].isEditing = !todoList.value[todoIndex].isEditing;
};

const updateTodo = (todoVal, todoIndex) => {
  todoList.value[todoIndex].todo = todoVal;
};

const deleteTodo = (todoId) => {
  todoList.value = todoList.value.filter((todo) => todo.id !== todoId);
};
</script>

<template>
  <main>
    <h1>Create Todos</h1>
    <TodoCreator @create-todo="createTodo" />
    <ul class="todo-list" v-if="todoList.length > 0">
      <TodoItem v-for="(todo, index) in todoList" :todo="todo" :index="index" @toggle-complete="toggleTodoComplete"
        @edit-todo="toggleEditTodo" @update-todo="updateTodo" @delete-todo="deleteTodo"/>
    </ul>
    <p class="todos-msg" v-else>
      <span>You have no Todos</span>
      <Icon icon="noto-v1:sad-but-relieved-face" width="22" height="22" />
    </p>

    <p v-if="todoCompleted && todoList.length > 0" class="todos-msg">
      <Icon icon="noto-v1:party-popper"></Icon>
      <span>You have completed all your tasks!</span>
    </p>
  </main>
</template>

<style lang="scss" scoped>
main {
  display: flex;
  flex-direction: column;
  max-width: 500px;
  width: 100%;
  margin: 0 auto;
  padding: 40px 16px;

  h1 {
    margin-bottom: 16px;
    text-align: center;
  }

  .todo-list {
    display: flex;
    flex-direction: column;
    list-style: none;
    margin-top: 24px;
    gap: 20px;
  }

  .todos-msg {
    display: flex;
    align-items: center;
    justify-content: center;
    gap: 8px;
    margin-top: 24px;
  }
}
</style>