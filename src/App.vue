<script setup>
import { ref } from 'vue'
import HomeView from './views/HomeView.vue'
import ChipsWheel from './components/ChipsWheel.vue'

const showSplash = ref(true)
const isTransitioning = ref(false)

const handleStartCooking = () => {
  isTransitioning.value = true
  setTimeout(() => {
    showSplash.value = false
    isTransitioning.value = false
  }, 200) // Duration matches the CSS transition
}
</script>

<template>
  <div class="app-container">
    <Transition
      name="dissolve"
      mode="out-in"
      @before-enter="isTransitioning = true"
      @after-enter="isTransitioning = false"
    >
      <ChipsWheel v-if="showSplash" @start-cooking="handleStartCooking" key="splash" />
      <HomeView v-else key="home" />
    </Transition>
  </div>
</template>

<style scoped>
.app-container {
  width: 100%;
  height: 100vh;
  overflow: hidden;
}

/* Dissolve transition */
.dissolve-enter-active,
.dissolve-leave-active {
  transition: opacity 0.3s ease-in-out;
}

.dissolve-enter-from {
  opacity: 0;
}

.dissolve-leave-to {
  opacity: 0;
}

.dissolve-enter-to,
.dissolve-leave-from {
  opacity: 1;
}

header {
  line-height: 1.5;
  max-height: 100vh;
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
    display: flex;
    place-items: center;
    padding-right: calc(var(--section-gap) / 2);
  }

  .logo {
    margin: 0 2rem 0 0;
  }

  header .wrapper {
    display: flex;
    place-items: flex-start;
    flex-wrap: wrap;
  }

  nav {
    text-align: left;
    margin-left: -1rem;
    font-size: 1rem;

    padding: 1rem 0;
    margin-top: 1rem;
  }
}
</style>
