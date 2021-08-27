<template>
  <div class="todo-item">
    <button class="checkbox" @click="toggleDone">
      <CheckmarkIcon v-if="todo.done" class="w-4" />
    </button>
    <div class="flex-grow" @dblclick="startEditing">
      <div v-if="!editing" class="text-wrapper" :class="{ 'text-done': todo.done }">
        {{ todo.text }}
      </div>
      <form v-else @submit.prevent="commitEdit">
        <input
          type="text"
          v-model="editedText"
          class="input"
          @blur="commitEdit"
          autofocus
        />
      </form>
    </div>
    <VueButton variant="danger" class="ml-2">
      <DeleteIcon class="w-5 h-5 -mx-2" @click="$emit('deleteTodo', $event)" />
    </VueButton>
  </div>
</template>

<script lang="ts">
import { defineComponent, onMounted, PropType, ref } from 'vue';

import VueButton from './Button.vue';
import CheckmarkIcon from './CheckmarkIcon.vue';
import DeleteIcon from './DeleteIcon.vue';
import { Todo } from '@/interfaces';

export default defineComponent({
  name: 'TodoIdem',
  props: {
    todo: { type: Object as PropType<Todo>, required: true }
    // Prop type is something that JS evaluates - runtime validation
    // Casting a constructor with PropType
  },
  emits: ['doneUpdated', 'textEdited', 'deleteTodo'],
  components: {
    VueButton,
    CheckmarkIcon,
    DeleteIcon
  },
  setup(props, { emit }) {
    // First optional argument of setup function - props
    // Props are reactive and can be watched (if you use ES6 destructuring it will remove the reactivity)
    // Context - second optional argument (exposes 3 component properties)
    const editing = ref(false);
    const editedText = ref('');

    function toggleDone() {
      emit('doneUpdated', !props.todo.done);
    }

    function startEditing() {
      editing.value = true;
      editedText.value = props.todo.text;
    }

    function commitEdit() {
      emit('textEdited', editedText.value);
      editing.value = false;
    }

    // Life-cycle hooks registered inside of the setup function
    onMounted(() => console.log(props.todo.text));

    return {
      editing,
      editedText,
      toggleDone,
      startEditing,
      commitEdit
    };
  }
});
</script>

<style lang="postcss" scoped>
.todo-item {
  @apply flex items-center px-4 py-2 rounded-md;
  &:hover {
    @apply bg-gray-50;
  }
}
.text-wrapper {
  @apply py-2 px-3 text-base;
}

.text-done {
  @apply text-gray-400 line-through;
}

.checkbox {
  @apply w-8 h-8 rounded-full border border-green-400 text-green-400;
  @apply flex justify-center items-center mr-2;

  &:hover,
  &:focus {
    @apply bg-green-50;
  }

  &:active {
    @apply border-green-400 bg-green-400;
  }
}

.input {
  @apply w-full bg-transparent py-2 px-3 flex items-center border-none;
  box-shadow: 0 0 3px theme('colors.green.400');
  &:focus {
    @apply outline-none;
  }

  &::placeholder {
    @apply text-gray-400 opacity-100;
  }
}
</style>
