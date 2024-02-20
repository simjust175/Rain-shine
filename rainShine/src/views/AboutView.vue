<template>
  <div class="about">
    <h1>This is an about page</h1>
    <to-erase />
  </div>
</template>

<script setup>
import ToErase from '@/components/ToErase.vue';
import { defineComponent } from 'vue';
defineComponent({ ToErase })

</script>
<style>
@media (min-width: 1024px) {
  .about {
    min-height: 100vh;
    display: flex;
    align-items: center;
  }
}
</style>
