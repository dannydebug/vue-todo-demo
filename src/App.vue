<template>
  <div class="h-screen">
    <div class="w-1/2 mx-auto">
      <h1 class="title" @click="shuffle">TODOS</h1>
      <div class="card-wrapper">
        <form class="flex" @submit.prevent="addTodo()">
          <div class="flex-grow">
            <input
              type="text"
              placeholder="Write what needs to be done"
              v-model="todoText"
              class="input"
            />
          </div>
          <VueButton class="ml-2">Add</VueButton>
        </form>
        <ul class="list">
          <transition-group name="todo-list">
            <li v-for="todo in todos" :key="todo.id" class="list-item">
              <TodoItem
                :todo="todo"
                @done-updated="todo.done = $event"
                @text-edited="updateText(todo, $event)"
                @delete-todo="deleteTodo(todo)"
              />
            </li>
          </transition-group>
        </ul>
      </div>
    </div>
  </div>
</template>

<script lang="ts">
import { defineComponent, ref, watch, watchEffect } from 'vue';

import TodoItem from './components/TodoItem.vue';
import VueButton from './components/Button.vue';
import { Todo } from './interfaces';

export default defineComponent({
  name: 'App',
  components: {
    TodoItem,
    VueButton
  },
  setup() {
    // TODO: What is a ref?
    // function ref<ValueType>(value: ValueType) {
    //   return reactive({ value });
    // }
    // const hello1 = reactive({ value: 'Hello' });
    // hello1.value;
    // const hello2 = ref('Hello');
    // hello2.value;

    const todos = ref<Array<Todo>>([]);
    const todoText = ref<string | null>(null);

    function addTodo() {
      if (todoText.value !== null && todoText.value.length > 3) {
        todos.value.push({
          id: `todo-id-${Math.floor(Math.random() * 1e9)}`,
          text: todoText.value,
          done: false
        });

        todoText.value = null;
      }
    }

    function updateText(todo: Todo, text: string) {
      todo.text = text;
      if (todo.text === '') {
        deleteTodo(todo);
      }
    }

    function deleteTodo(todo: Todo) {
      const index = todos.value.indexOf(todo);
      todos.value.splice(index, 1);
    }

    function shuffle() {
      todos.value.sort(() => Math.random() - 0.5);
    }

    watch(todos, () => {
      console.log(`Current number of todos: ${todos.value.length}`);
    });

    watchEffect(() => {
      console.log(`Current number of todos: ${todos.value.length}`);
    });

    return {
      todos,
      todoText,
      addTodo,
      deleteTodo,
      updateText,
      shuffle
    };
  }
});
</script>

<style lang="postcss">
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
}

.list {
  @apply mt-4;
}

.title {
  @apply mt-20 text-4xl font-semibold text-green-400 text-center;
  @apply cursor-pointer;
}

.card-wrapper {
  @apply border border-gray-300 shadow-md rounded-md p-4 mt-8;
}

.input {
  @apply w-full appearance-none bg-transparent py-2 px-3 flex items-center;
  @apply border border-gray-200 rounded-md;

  &:focus {
    @apply outline-none;
  }

  &:focus-within {
    box-shadow: 0 0 6px theme('colors.green.500');
  }

  &::placeholder {
    @apply text-gray-400 opacity-100;
  }
}

.todo-list-enter-active,
.todo-list-leave-active {
  transition: all 1s ease;
}
.todo-list-enter-from,
.todo-list-leave-to {
  opacity: 0;
  transform: translateX(-30px);
}
.todo-list-move {
  transition: transform 0.7s ease;
}
</style>
