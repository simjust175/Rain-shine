<script setup>
import { RouterView } from 'vue-router';
import { ref, computed } from 'vue';
let imgType = ref('svg');
const imgSrc = computed(()=> `../../public/RainDrop_blue.${imgType.value}`)
</script>

<template>
<div class="logo"  @mouseenter="imgType='gif'" @mouseleave="imgType='svg'"><img :src="imgSrc" alt="RainDrop"></div>

  <RouterView />
</template>

<style scoped>
.logo {
    position: absolute;
    top: -93px;
    left: -22px;
}

.logo img{  
    height: 250px;
}

.logo:hover {
  cursor: pointer;
}

header {
  line-height: 1.5;
  max-height: 100vh;
  /* background: rgb(43, 167, 255); */
}

.logo {
  display: block;
  margin: 0 auto 2rem;
}

nav {
  width: 100%;
  font-size: 12px;
  text-align: center;
  margin-top: 2rem;
}

nav a.router-link-exact-active {
  color: var(--color-text);
}

nav a.router-link-exact-active:hover {
  background-color: transparent;
}

nav a {
  display: inline-block;
  padding: 0 1rem;
  border-left: 1px solid var(--color-border);
}

nav a:first-of-type {
  border: 0;
}

@media (min-width: 1024px) {
  header {
    padding-right: calc(var(--section-gap) / 2);
  }

  .logo {
    margin: 0 2rem 0 0;
  }

  /* header .wrapper {
    display: flex;
    place-items: flex-start;
    flex-wrap: wrap;
  } */

  nav {
    text-align: left;
    margin-left: -1rem;
    font-size: 1rem;

    padding: 1rem 0;
    margin-top: 1rem;
  }
}
</style>
