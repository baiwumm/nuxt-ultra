<script setup lang="ts">
import { motion } from 'motion-v'

const { x, y } = useMouse()
</script>

<template>
  <div class="flex flex-col h-screen s-screen items-center justify-center gap-4">
    <div class="text-lg font-bold">
      Nuxt.js
    </div>
    <div>pos: {{ x }}, {{ y }}</div>
    <Button>Click me</Button>
    <motion.div
      :while-hover="{ scale: 1.2 }"
      :while-press="{ scale: 0.8 }"
      class="box"
    />
  </div>
</template>

<style>
  .box {
    width: 100px;
    height: 100px;
    background-color: #0d63f8;
    border-radius: 10px;
  }
</style>
