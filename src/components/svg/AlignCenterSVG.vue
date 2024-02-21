<template>
  <svg
    width="18"
    height="17"
    viewBox="0 0 18 17"
    fill="none"
    xmlns="http://www.w3.org/2000/svg"
  >
    <path
      d="M0 0H18V2H0V0ZM2 15H16V17H2V15ZM0 10H18V12H0V10ZM2 5H16V7H2V5Z"
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
