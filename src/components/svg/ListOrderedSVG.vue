<template>
  <svg
    width="18"
    height="18"
    viewBox="0 0 18 18"
    fill="none"
    xmlns="http://www.w3.org/2000/svg"
  >
    <path
      d="M5 1H18V3H5V1ZM2 0V3H3V4H0V3H1V1H0V0H2ZM0 11V8.5H2V8H0V7H3V9.5H1V10H3V11H0ZM2 16.5H0V15.5H2V15H0V14H3V18H0V17H2V16.5ZM5 8H18V10H5V8ZM5 15H18V17H5V15Z"
      :fill="color || 'var(--svg-color)'"
    />
  </svg>
</template>

<script setup>
defineProps({
  color: {
    type: String,
    default: '',
  },
});
</script>
