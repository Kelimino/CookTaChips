<template>
  <div class="ingredient-tag" @click="removeIngredient">
    <span class="ingredient-icon">✕</span>
    <span class="ingredient-name">{{ ingredient.name }}</span>
  </div>
</template>

<script setup>
const props = defineProps({
  ingredient: {
    type: Object,
    required: true,
  },
})

const emit = defineEmits(['remove'])

const removeIngredient = () => {
  emit('remove', props.ingredient.id)
}
</script>

<style scoped>
.ingredient-tag {
  display: inline-flex;
  align-items: center;
  gap: 0.5rem;
  background: rgba(255, 255, 255, 0.9);
  border-radius: 20px;
  padding: 0.5rem 0.75rem;
  margin: 0.25rem;
  cursor: pointer;
  transition: all 0.2s ease;
}

.ingredient-tag:hover {
  background: rgba(255, 255, 255, 1);
  transform: scale(0.95);
}

.ingredient-icon {
  width: 16px;
  height: 16px;
  background: rgba(0, 0, 0, 0.1);
  border-radius: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 10px;
  font-weight: bold;
  color: #666;
}

.ingredient-name {
  font-size: 0.85rem;
  font-weight: 500;
  color: #333;
}
</style>
