<template>
  <div class="method-container">
    <label for="C">      
      <input type="radio" id="C" name="method" value="C" class="peer radio" v-model="methodChoice" checked/>
      <div class="icon">C°</div>
    </label>
    <label for="F"> 
      <input type="radio" id="F" name="method" value="F" class="peer radio" v-model="methodChoice" />
      <div class="icon">F°</div>
    </label>
  </div>
</template>

<script setup>
import { ref, watch, defineEmits } from 'vue';

const methodChoice = ref('');
const emit = defineEmits(["methodChoice"]);

watch(methodChoice, (choice)=> emit('methodChoice', choice));

</script>

<style>
.method-container {
  display: flex;
}

/* 
HTML CSSResult Skip Results Iframe */
:root {
  --primary-color: lightblue;
  --icon-size: 3rem;
}

input[type="radio"] {
  display: inline-block;
  position: absolute;
  overflow: hidden;
  clip: rect(0 0 0 0);
  height: 1;
  width: 1;
  margin: -1;
  padding: 0;
  border: 0;
}

.icon {
  display: flex;
  justify-content: center;
  align-items: center;
  width: var(--icon-size);
  height: var(--icon-size);
  color: rgb(40, 152, 189);
  border-radius: 0.5rem;
  padding: 0.5rem;
  overflow: hidden;
}

.icon:hover {
  cursor: pointer;
  background-color: rgba(173, 216, 230, 0.1);
}

.peer:checked~.icon {
  background-color:rgb(173, 216, 230, 0.8);
}
</style>