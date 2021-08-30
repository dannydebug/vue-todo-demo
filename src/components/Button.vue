<template>
  <button class="v-btn" :class="classes">
    <slot />
  </button>
</template>

<script lang="ts">
import { computed, defineComponent } from 'vue';

export default defineComponent({
  name: 'VueButton',
  props: {
    variant: {
      type: String,
      default: 'primary',
      validator: (value: string) => ['primary', 'danger'].includes(value)
    },
    disabled: { type: Boolean }
  },
  setup(props) {
    const classes = computed(() => ({
      primary: props.variant === 'primary',
      danger: props.variant === 'danger',
      disabled: props.disabled
    }));

    return {
      classes
    };
  }
});
</script>

<style lang="postcss" scoped>
.v-btn {
  @apply rounded-md py-2 px-4 text-base font-medium;
  @apply transition duration-300 cursor-pointer;

  &.primary {
    @apply border border-green-400 text-green-400;

    &:hover,
    &:focus {
      @apply text-white bg-gradient-to-tr from-green-400 to-green-800;
    }

    &:active {
      @apply border-green-400 bg-green-400 text-white;
    }
  }

  &.danger {
    @apply border border-red-400 text-red-400;

    &:hover,
    &:focus {
      @apply bg-gradient-to-tr from-red-400 to-red-800 text-white;
    }

    &:active {
      @apply border-red-400 bg-red-400 text-white;
    }
  }

  &.disabled {
    @apply border border-gray-300;
    @apply bg-gray-400 text-gray-200 cursor-not-allowed;

    &:hover,
    &:focus {
      @apply border border-gray-300;
      @apply bg-gray-400 text-gray-200;
    }
  }
}
</style>
