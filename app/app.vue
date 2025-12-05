<script setup lang="ts">
const { x, y } = useMouse()
</script>

<template>
  <div class="flex flex-col h-screen s-screen items-center justify-center gap-4">
    <div class="text-lg font-bold">
      Nuxt.js
    </div>
    <div>pos: {{ x }}, {{ y }}</div>
    <Button>Click me</Button>
  </div>
</template>
