<template>
  <svg
    width="18"
    height="17"
    viewBox="0 0 18 17"
    fill="none"
    xmlns="http://www.w3.org/2000/svg"
  >
    <path
      d="M0 0H18V2H0V0ZM0 15H14V17H0V15ZM0 10H18V12H0V10ZM0 5H14V7H0V5Z"
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
