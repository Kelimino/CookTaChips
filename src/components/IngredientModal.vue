<template>
  <Transition appear @before-enter="onBeforeEnter" @enter="onEnter" @leave="onLeave" :css="false">
    <div v-if="isOpen" class="modal-overlay" @click="closeModal">
      <div class="modal-content" @click.stop>
        <div class="modal-header">
          <img class="modal-image" :src="image" alt="modal image" />
          <h3 class="modal-title">{{ title }}</h3>
        </div>
        <div class="ingredient-list">
          <div
            v-for="ingredient in ingredients"
            :key="ingredient.id"
            class="ingredient-item"
            @click="selectIngredient(ingredient)"
          >
            {{ ingredient.name }}
          </div>
        </div>
      </div>
    </div>
  </Transition>
</template>

<script setup>
import { gsap } from 'gsap'

const props = defineProps({
  isOpen: {
    type: Boolean,
    default: false,
  },
  title: {
    type: String,
    default: 'Sélectionnez un ingrédient',
  },
  image: {
    type: String,
    default: '',
  },
  ingredients: {
    type: Array,
    default: () => [],
  },
})

const emit = defineEmits(['close', 'select'])

const closeModal = () => {
  emit('close')
}

const selectIngredient = (ingredient) => {
  emit('select', ingredient)
  emit('close')
}

// GSAP animation methods
const onBeforeEnter = (el) => {
  const overlay = el
  const content = el.querySelector('.modal-content')
  
  gsap.set(overlay, { opacity: 0 })
  gsap.set(content, {
    opacity: 0,
    scale: 0.8
  })
}

const onEnter = (el, done) => {
  const overlay = el
  const content = el.querySelector('.modal-content')
  
  const tl = gsap.timeline({ onComplete: done })
  
  tl.to(overlay, {
    opacity: 1,
    duration: 0.2,
    ease: "power2.out"
  })
  .to(content, {
    opacity: 1,
    scale: 1,
    duration: 0.3,
    ease: "back.out(1.7)"
  }, "-=0.1")
}

const onLeave = (el, done) => {
  const overlay = el
  const content = el.querySelector('.modal-content')
  
  const tl = gsap.timeline({ onComplete: done })
  
  tl.to(content, {
    opacity: 0,
    scale: 0.8,
    duration: 0.2,
    ease: "power2.in"
  })
  .to(overlay, {
    opacity: 0,
    duration: 0.15,
    ease: "power2.in"
  }, "-=0.1")
}
</script>

<style scoped>
.modal-overlay {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: rgba(0, 0, 0, 0.5);
  display: flex;
  align-items: center;
  justify-content: center;
  z-index: 1000;
}

.modal-content {
  background: white;
  border-radius: 12px;
  padding: 0;
  min-width: 300px;
  max-width: 400px;
  max-height: 80vh;
  overflow: hidden;
  box-shadow: 0 10px 30px rgba(0, 0, 0, 0.3);
}

.modal-header {
  display: flex;
  align-items: center;
  gap: 8px;
  padding: 1rem;
  border-bottom: 1px solid #e0e0e0;
  text-align: center;
}

.modal-image {
  width: 54px;
  height: 54px;
  object-fit: contain;
}

.modal-title {
  margin: 0;
  font-size: 1.1rem;
  font-weight: 600;
  color: #333;
}

.ingredient-list {
  max-height: 300px;
  overflow-y: auto;
}

.ingredient-item {
  padding: 0.75rem 1rem;
  border-bottom: 1px solid #f0f0f0;
  cursor: pointer;
  transition: background-color 0.2s ease;
  font-size: 0.95rem;
  color: #333;
}

.ingredient-item:hover {
  background-color: #f5f5f5;
}

.ingredient-item:last-child {
  border-bottom: none;
}
</style>