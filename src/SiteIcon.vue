<script setup>
defineProps({
  icon: { type: [Object, Function], required: true },
});
</script>

<template>
  <svg v-if="icon.icon" :viewBox="`0 0 ${icon.icon[0]} ${icon.icon[1]}`" aria-hidden="true">
    <path v-for="path in [icon.icon[4]].flat()" :key="path" :d="path" />
  </svg>
  <component v-else :is="icon" class="outline-icon" :stroke-width="1.75" aria-hidden="true" />
</template>

<style scoped>
.outline-icon {
  fill: none;
}
</style>
