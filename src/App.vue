<script setup>
import { ref, onMounted } from 'vue'
import { gsap } from 'gsap'
import HomeView from './views/HomeView.vue'
import ChipsWheel from './components/ChipsWheel.vue'

const showSplash = ref(true)
const isTransitioning = ref(false)
const overlayPath = ref(null)

// SVG paths for the gooey transition
const paths = {
  step1: {
    unfilled: 'M 0 100 V 100 Q 50 100 100 100 V 100 z',
    inBetween: {
      curve1: 'M 0 100 V 50 Q 50 0 100 50 V 100 z',
      curve2: 'M 0 100 V 50 Q 50 100 100 50 V 100 z',
    },
    filled: 'M 0 100 V 0 Q 50 0 100 0 V 100 z',
  },
  step2: {
    filled: 'M 0 0 V 100 Q 50 100 100 100 V 0 z',
    inBetween: {
      curve1: 'M 0 0 V 50 Q 50 0 100 50 V 0 z',
      curve2: 'M 0 0 V 50 Q 50 100 100 50 V 0 z',
    },
    unfilled: 'M 0 0 V 0 Q 50 0 100 0 V 0 z',
  },
}

const handleStartCooking = () => {
  if (isTransitioning.value) return
  isTransitioning.value = true

  // Start the gooey transition from splash to home
  gsap
    .timeline({
      onComplete: () => {
        isTransitioning.value = false
      },
    })
    .set(overlayPath.value, {
      attr: { d: paths.step1.unfilled },
    })
    .to(
      overlayPath.value,
      {
        duration: 0.8,
        ease: 'power4.in',
        attr: { d: paths.step1.inBetween.curve1 },
      },
      0,
    )
    .to(overlayPath.value, {
      duration: 0.2,
      ease: 'power1',
      attr: { d: paths.step1.filled },
      onComplete: () => {
        showSplash.value = false
      },
    })
    .set(overlayPath.value, {
      attr: { d: paths.step2.filled },
    })
    .to(overlayPath.value, {
      duration: 0.2,
      ease: 'sine.in',
      attr: { d: paths.step2.inBetween.curve1 },
    })
    .to(overlayPath.value, {
      duration: 1,
      ease: 'power4',
      attr: { d: paths.step2.unfilled },
    })
}

const goBackToStart = () => {
  if (isTransitioning.value || showSplash.value) return
  isTransitioning.value = true

  // Start the gooey transition from home back to splash
  gsap
    .timeline({
      onComplete: () => {
        isTransitioning.value = false
      },
    })
    .set(overlayPath.value, {
      attr: { d: paths.step2.unfilled },
    })
    .to(
      overlayPath.value,
      {
        duration: 0.8,
        ease: 'power4.in',
        attr: { d: paths.step2.inBetween.curve2 },
      },
      0,
    )
    .to(overlayPath.value, {
      duration: 0.2,
      ease: 'power1',
      attr: { d: paths.step2.filled },
      onComplete: () => {
        showSplash.value = true
      },
    })
    .set(overlayPath.value, {
      attr: { d: paths.step1.filled },
    })
    .to(overlayPath.value, {
      duration: 0.2,
      ease: 'sine.in',
      attr: { d: paths.step1.inBetween.curve2 },
    })
    .to(overlayPath.value, {
      duration: 1,
      ease: 'power4',
      attr: { d: paths.step1.unfilled },
    })
}
</script>

<template>
  <div class="app-container">
    <!-- SVG Overlay for gooey transition -->
    <div class="overlay" :class="{ 'overlay--active': isTransitioning }">
      <svg class="overlay__svg" viewBox="0 0 100 100" preserveAspectRatio="none">
        <defs>
          <linearGradient id="gooeyGradient" x1="0%" y1="0%" x2="100%" y2="100%">
            <stop offset="0%" style="stop-color: var(--yellow); stop-opacity: 1" />
            <stop offset="100%" style="stop-color: var(--brown); stop-opacity: 1" />
          </linearGradient>
        </defs>
        <path
          ref="overlayPath"
          class="overlay__path"
          d="M 0 100 V 100 Q 50 100 100 100 V 100 z"
          fill="url(#gooeyGradient)"
        />
      </svg>
    </div>

    <!-- Header -->
    <header class="app-header">
      <a href="#" class="header-logo-link" @click.prevent="goBackToStart">
        <img src="/images/CookTaChips.svg" alt="CookTaChips" class="header-logo" />
      </a>
      <a
        href="https://www.kellig.fr/"
        target="_blank"
        rel="noopener noreferrer"
        class="website-link"
      >
        <img src="/images/kellig.svg" alt="Kellig.fr" class="website-logo" />
      </a>
    </header>

    <!-- Content Views -->
    <div class="content-container">
      <ChipsWheel
        v-if="showSplash"
        @start-cooking="handleStartCooking"
        class="view view--splash"
        :class="{ 'view--hidden': isTransitioning && !showSplash }"
      />
      <HomeView
        v-if="!showSplash"
        class="view view--home"
        :class="{ 'view--hidden': isTransitioning && showSplash }"
      />
    </div>
  </div>
</template>

<style scoped>
:root {
  --yellow: #fbdb93;
  --brown: #cbb37e;
}

.app-container {
  width: 100%;
  height: 100vh;
  overflow: hidden;
  position: relative;
}

.content-container {
  width: 100%;
  height: 100vh;
  position: relative;
}

.view {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  transition: opacity 0.3s ease;
}

.view--hidden {
  opacity: 0;
  pointer-events: none;
}

/* SVG Overlay Styles */
.overlay {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  pointer-events: none;
  z-index: 1000;
  opacity: 0;
  transition: opacity 0.1s ease;
}

.overlay--active {
  opacity: 1;
}

.overlay__svg {
  width: 100%;
  height: 100%;
}

.overlay__path {
  fill: url(#gooeyGradient);
}

/* Header Styles */
.app-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 1rem 2rem;
  width: 100%;
  z-index: 100;
  position: fixed;
  top: 0;
}

.header-logo-link {
  text-decoration: none;
  transition: transform 0.2s ease;
  cursor: pointer;
}

.header-logo-link:hover {
  transform: scale(1.05);
}

.header-logo {
  height: 40px;
  width: auto;
  object-fit: contain;
}

.website-link {
  text-decoration: none;
  transition: transform 0.2s ease;
}

.website-link:hover {
  transform: scale(1.05);
}

.website-logo {
  height: 24px;
  width: auto;
  object-fit: contain;
}

/* Responsive Design */
@media (min-width: 1024px) {
  .app-header {
    padding: 1.5rem 3rem;
  }

  .header-logo {
    height: 48px;
  }

  .website-logo {
    height: 28px;
  }
}
</style>
