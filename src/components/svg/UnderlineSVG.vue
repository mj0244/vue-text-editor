<template>
  <svg
    width="16"
    height="19"
    viewBox="0 0 16 19"
    fill="none"
    xmlns="http://www.w3.org/2000/svg"
  >
    <path
      d="M4 0V9C4 10.0609 4.42143 11.0783 5.17157 11.8284C5.92172 12.5786 6.93913 13 8 13C9.06087 13 10.0783 12.5786 10.8284 11.8284C11.5786 11.0783 12 10.0609 12 9V0H14V9C14 10.5913 13.3679 12.1174 12.2426 13.2426C11.1174 14.3679 9.5913 15 8 15C6.4087 15 4.88258 14.3679 3.75736 13.2426C2.63214 12.1174 2 10.5913 2 9V0H4ZM0 17H16V19H0V17Z"
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
