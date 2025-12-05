<script setup lang="ts">
import type { PrimitiveProps } from 'reka-ui'
import type { HTMLAttributes } from 'vue'
import type { ButtonVariants } from '.'
import { motion } from 'motion-v'
import { Primitive } from 'reka-ui'
import { cn } from '@/lib/utils'

import { buttonVariants } from '.'

interface Props extends PrimitiveProps {
  variant?: ButtonVariants['variant']
  size?: ButtonVariants['size']
  class?: HTMLAttributes['class']
  hoverScale?: number
  tapScale?: number
  rippleColor?: string
  duration?: number
}

const props = withDefaults(defineProps<Props>(), {
  as: 'button',
  hoverScale: 1.05,
  tapScale: 0.95,
  rippleColor: 'var(--primary-foreground)',
  duration: 500,
})

const emit = defineEmits<{
  (e: 'click', event: MouseEvent): void
}>()

const MotionComponent = motion.create(Primitive)

const buttonRipples = ref<Array<{ x: number, y: number, size: number, key: number }>>([])

function createRipple(event: MouseEvent) {
  const button = event.currentTarget as HTMLElement | null
  if (!button)
    return

  const rect = button.getBoundingClientRect()
  const size = Math.max(rect.width, rect.height)
  const x = event.clientX - rect.left - size / 2
  const y = event.clientY - rect.top - size / 2

  const newRipple = { x, y, size, key: Date.now() }
  buttonRipples.value.push(newRipple)
}

function handleClick(event: MouseEvent) {
  createRipple(event)
  emit('click', event)
}

watchEffect(() => {
  if (buttonRipples.value.length > 0) {
    const lastRipple = buttonRipples.value[buttonRipples.value.length - 1]
    setTimeout(() => {
      buttonRipples.value = buttonRipples.value.filter(ripple => ripple.key !== lastRipple?.key)
    }, props.duration)
  }
})
</script>

<template>
  <MotionComponent
    data-slot="button"
    :as="as"
    :as-child="asChild"
    :class="cn(buttonVariants({ variant, size }), props.class)"
    :while-hover="{ scale: hoverScale }"
    :while-press="{ scale: tapScale }"
    :style="{ '--duration': `${$props.duration}ms` }"
    class="relative overflow-hidden"
    @click="handleClick"
  >
    <div class="relative z-10">
      <slot />
    </div>

    <span class="pointer-events-none absolute inset-0">
      <span
        v-for="ripple in buttonRipples"
        :key="ripple.key"
        class="ripple-animation absolute rounded-full bg-background opacity-30"
        :style="{
          width: `${ripple.size}px`,
          height: `${ripple.size}px`,
          top: `${ripple.y}px`,
          left: `${ripple.x}px`,
          backgroundColor: $props.rippleColor,
          transform: 'scale(0)',
          animationDuration: `${$props.duration}ms`,
        }"
      />
    </span>
  </MotionComponent>
</template>

<style scoped>
@keyframes rippling {
  0% {
    opacity: 1;
  }
  100% {
    transform: scale(2);
    opacity: 0;
  }
}

.ripple-animation {
  animation: rippling var(--duration) ease-out;
}
</style>
