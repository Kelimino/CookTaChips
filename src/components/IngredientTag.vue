<template>
  <div
    class="ingredient-tag"
    @click="handleRemove"
    :title="`Cliquer pour retirer ${ingredient.name}`"
  >
    <span class="ingredient-name">{{ ingredient.name }}</span>
    <button class="remove-button" @click.stop="handleRemove" aria-label="Retirer cet ingrédient">
      ×
    </button>
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

const handleRemove = () => {
  emit('remove', props.ingredient.id)
}
</script>

<style scoped lang="scss">
.ingredient-tag {
  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: 0.5rem;
  background: white;
  color: var(--purple);
  border: 1px solid #e0e0e0;
  border-radius: 20px;
  padding: 0.5rem 0.75rem;
  font-size: 0.9rem;
  font-weight: 500;
  cursor: pointer;
  transition: all 0.2s ease;
  user-select: none;
  min-width: fit-content;
}

.ingredient-tag:hover {
  background: #f8f9fa;
  border-color: #d0d0d0;
  transform: translateY(-1px);
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
}

.ingredient-tag:active {
  transform: translateY(0);
  box-shadow: 0 1px 4px rgba(0, 0, 0, 0.1);
}

.ingredient-name {
  flex: 1;
  white-space: nowrap;
}

.remove-button {
  background: none;
  border: none;
  color: #999;
  font-size: 1.2rem;
  font-weight: bold;
  cursor: pointer;
  padding: 0;
  width: 20px;
  height: 20px;
  display: flex;
  align-items: center;
  justify-content: center;
  border-radius: 50%;
  transition: all 0.2s ease;
  line-height: 1;
}

.remove-button:hover {
  background: #f0f0f0;
  color: #666;
}

.remove-button:active {
  background: #e0e0e0;
}
</style>
