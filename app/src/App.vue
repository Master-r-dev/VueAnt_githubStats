<script setup lang="ts"> 
import Main from './components/Main.vue'
</script>

<template>
  <header>
    <img alt="Vue logo" class="logo" src="./assets/logo.svg" width="125" height="125" />
  </header>

  <main>
    <Main />
  </main>
</template>

<style scoped>
#app {
	 font-family: "Avenir", Helvetica, Arial, sans-serif;
	 -webkit-font-smoothing: antialiased;
	 -moz-osx-font-smoothing: grayscale;
	 color: #2c3e50;
	 display: flex;
	 flex-flow: column nowrap;
	 justify-content: flex-start;
	 align-items: flex-start;
	 padding: 2%;
	 width: 100%;
}

header {
  line-height: 1.5;
}

.logo {
  display: block;
  margin: 0 auto 2rem;
}

@media (min-width: 1024px) {
  header {
    display: flex;
    place-items: center;
    padding-right: calc(var(--section-gap) / 2);
  }

  .logo {
    margin: 0 2rem 0 0;
  } 
}
</style>
