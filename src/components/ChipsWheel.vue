<template>
  <div class="wrapper">
    <div class="content">
      <h1><strong>Cook</strong> <em>ta</em> Chips !</h1>
      <button @click="handleStartCooking">
        <img src="../../public/images/chef.png" alt="" />
        En cuisine
      </button>
    </div>
    <div class="overlay-gradient"></div>
    <div class="container" ref="container">
      <svg viewBox="0 0 400 400">
        <path
          stroke-width="2"
          stroke="transparent"
          id="myPath"
          fill="none"
          d="M350,200 C350,282.84 282.84,350 200,350 117.16,350 50,282.84 50,200 50,117.16 117.16,50 200,50 282.84,50 350,117.16 350,200 z"
        ></path>
      </svg>

      <div v-for="(img, index) in chips" :key="index" class="chip">
        <img :src="img" alt="chips bag" />
      </div>
    </div>
  </div>
</template>

<script setup>
import { onMounted, ref } from 'vue'
import { gsap } from 'gsap'
import { Draggable } from 'gsap/Draggable'
import { InertiaPlugin } from 'gsap/InertiaPlugin'
import { MotionPathPlugin } from 'gsap/MotionPathPlugin'

// Register GSAP plugins
gsap.registerPlugin(Draggable, InertiaPlugin, MotionPathPlugin)

// Define emits
const emit = defineEmits(['start-cooking'])

// Handle button click
const handleStartCooking = () => {
  emit('start-cooking')
}

const container = ref(null)
const chips = [
  '../images/bag-1.png',
  '../images/bag-2.png',
  '../images/bag-3.png',
  '../images/bag-4.png',
  '../images/bag-5.png',
  '../images/bag-6.png',
  '../images/bag-7.png',
  '../images/bag-8.png',
  '../images/bag-9.png',
  '../images/bag-10.png',
  '../images/bag-11.png',
  '../images/bag-12.png',
]

onMounted(() => {
  const bags = gsap.utils.toArray('.chip')
  let currentRotation = 0
  let autoRotationTween = null

  // Position bags along the circular path with proper alignment
  gsap.set(bags, {
    motionPath: {
      path: '#myPath',
      align: '#myPath',
      alignOrigin: [0.5, 1], // Align from bottom center of bags
      start: 0,
      end: (i) => i / bags.length,
      autoRotate: true, // Enable auto rotation to follow path
    },
  })

  // Create infinite rotation animation
  const startAutoRotation = () => {
    autoRotationTween = gsap.to(container.value, {
      rotation: '+=360',
      duration: 100, // Adjust speed (higher = slower)
      ease: 'none',
      repeat: -1,
      onUpdate: function () {
        currentRotation = this.targets()[0]._gsap.rotation || 0
      },
    })
  }

  // Start the auto rotation
  startAutoRotation()

  // Create draggable with improved handling
  const draggable = Draggable.create(container.value, {
    type: 'rotation',
    inertia: true,
    onDrag: function () {
      currentRotation = this.rotation
    },
    onThrowComplete: function () {
      // Resume auto rotation after interaction ends
      currentRotation = this.rotation
      gsap.set(container.value, { rotation: currentRotation })

      // Restart auto rotation from current position
      if (autoRotationTween) {
        autoRotationTween.kill()
      }
      autoRotationTween = gsap.to(container.value, {
        rotation: currentRotation + 360,
        duration: 100,
        ease: 'none',
        repeat: -1,
        onUpdate: function () {
          currentRotation = this.targets()[0]._gsap.rotation || 0
        },
      })
    },
  })[0]

  // Add scroll wheel support
  const handleWheel = (event) => {
    event.preventDefault()
    const delta = event.deltaY

    currentRotation += delta * 0.5

    gsap.to(container.value, {
      rotation: currentRotation,
      duration: 0.3,
      ease: 'power2.out',
      onComplete: function () {
        // Resume auto rotation after scroll ends
        setTimeout(() => {
          if (autoRotationTween) {
            autoRotationTween.kill()
          }
          autoRotationTween = gsap.to(container.value, {
            rotation: currentRotation + 360,
            duration: 20,
            ease: 'none',
            repeat: -1,
            onUpdate: function () {
              currentRotation = this.targets()[0]._gsap.rotation || 0
            },
          })
        }, 500) // Wait 500ms after scroll stops
      },
    })
  }

  // Add wheel event listener
  container.value.addEventListener('wheel', handleWheel, { passive: false })

  // Cleanup on unmount
  return () => {
    container.value?.removeEventListener('wheel', handleWheel)
    if (autoRotationTween) {
      autoRotationTween.kill()
    }
    if (draggable) {
      draggable.kill()
    }
  }
})
</script>

<style scoped lang="scss">
.wrapper {
  width: 100%;
  height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;
  overflow: hidden;
  background: var(--yellow);
}

.container {
  width: 100%;
  height: 100%;
  position: relative;
  transform: translateY(30%);
}

svg {
  width: 100%;
  height: 100%;
  position: absolute;
  top: 0;
  left: 0;
}

.chip {
  width: 300px;
  height: 300px;
  position: absolute;
  top: 0;
  left: 0;
  transform-origin: center bottom;
}

.chip img {
  width: 80%;
  height: auto;
}

.content {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  display: flex;
  justify-content: center;
  align-items: center;
  flex-direction: column;
  z-index: 2;
}

.content h1 {
  font-family: 'Dela Gothic One', 'Arial Black', sans-serif;
  font-size: 6rem;
  color: #fff;
  margin-bottom: 20px;

  em {
    font-size: 4rem;
  }
}

button {
  background: var(--purple);
  color: #fff;
  border: none;
  padding: 10px 20px;
  border-radius: 50px;
  font-weight: normal;
  font-style: normal;
  font-display: swap;
  font-size: 20px;
  display: flex;
  align-items: center;
  gap: 10px;
  cursor: pointer;
  transition: all 0.3s ease;
  max-height: 64px;

  img {
    transform: scale(1.2);
    width: 40px;
  }
}
button:hover {
  transform: scale(1.05);
  box-shadow: 0 6px 20px rgba(44, 62, 80, 0.4);
}

.overlay-gradient {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100vh;
  background: linear-gradient(to bottom, transparent 0%, #fbdb93 100%);
  pointer-events: none;
  z-index: 1;
}
</style>
