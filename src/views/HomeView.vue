<template>
  <div class="home-container">
    <!-- Main Content -->
    <div class="main-content">
      <!-- Cooking Pan -->
      <div class="pan-container">
        <img
          src="/images/cooking-pan.png"
          alt="Cooking Pan"
          class="cooking-pan"
          :class="{ wiggle: selectedIngredients.length > 0 }"
        />

        <!-- Lottie Smoke Animation -->
        <div v-if="selectedIngredients.length > 0" class="smoke-animation">
          <Vue3Lottie
            :animationData="smokeAnimation"
            :height="200"
            :width="200"
            :loop="true"
            :autoPlay="true"
          />
        </div>
      </div>

      <!-- Instruction Text -->
      <p class="instruction-text">Sélectionne dabord tes ingrédients</p>
      <!-- Selected Ingredients Display -->
      <div v-if="selectedIngredients.length > 0" class="ingredients-in-pan">
        <IngredientTag
          v-for="ingredient in selectedIngredients"
          :key="ingredient.id"
          :ingredient="ingredient"
          @remove="removeIngredient"
        />
      </div>
      <!-- Bottom App Bar (Dock Style) -->
      <div class="bottom-app-bar">
        <div class="dock-container">
          <!-- 3D Cookbook -->
          <div class="dock-item chef-cook" @click="openModal('chef')">
            <img
              src="/images/chef-cook.png"
              alt="Livre de cuisine"
              class="dock-icon chef-cook-icon"
            />
          </div>

          <!-- 5 Modal Icons -->
          <div class="dock-item" @click="openModal('vegetables')">
            <img src="/images/vegetables-icon.png" alt="Légumes" class="dock-icon" />
          </div>

          <div class="dock-item" @click="openModal('spices')">
            <img src="/images/spices-icon.png" alt="spices" class="dock-icon" />
          </div>

          <div class="dock-item" @click="openModal('dairy')">
            <img src="/images/dairy-icon.png" alt="Produits laitiers" class="dock-icon" />
          </div>

          <div class="dock-item" @click="openModal('meat')">
            <img src="/images/meat-icon.png" alt="Viandes" class="dock-icon" />
          </div>

          <div class="dock-item" @click="openModal('sauce')">
            <img src="/images/sauce-icon.png" alt="sauce" class="dock-icon" />
          </div>

          <!-- 3D Cookbook -->
          <div class="dock-item cookbook" @click="openModal('cookbook')">
            <img
              src="/images/cookbook-3d.png"
              alt="Livre de cuisine"
              class="dock-icon cookbook-icon"
            />
          </div>
        </div>
      </div>

      <!-- Bottom Action Button -->
      <div class="bottom-action">
        <!-- Dice Lottie Animation -->
        <div
          class="dice-lottie-container"
          @click="randomCombination"
          @mouseenter="playDiceAnimation"
        >
          <Vue3Lottie
            v-if="diceAnimation"
            :animationData="diceAnimation"
            :height="80"
            :width="80"
            :loop="false"
            :autoPlay="false"
            ref="diceAnimationRef"
          />
        </div>

        <button class="action-button" @click="startCooking">
          <img src="/images/oven-icon.png" alt="Four" class="action-icon" />
          <span class="action-text">C'est prêt ! </span>
        </button>
      </div>
    </div>

    <!-- Ingredient Selection Modal -->
    <IngredientModal
      :isOpen="modalState.isOpen"
      :title="modalState.title"
      :image="modalState.image"
      :ingredients="modalState.ingredients"
      @close="closeModal"
      @select="selectIngredient"
    />

    <!-- Loading Screen -->
    <div v-if="loadingState.isLoading" class="loading-overlay" ref="loadingOverlay">
      <!-- Central Chips Image -->
      <div class="central-chips">
        <img src="/images/chips-center.png" alt="Chips" class="chips-image" />

        <!-- Rotating Icons Around Chips -->
        <!-- <div class="rotating-icons">
            <div class="rotating-icon icon-1">
              <img src="/images/chef-hat-3d.png" alt="Chef Hat" />
            </div>
            <div class="rotating-icon icon-2">
              <img src="/images/spatula-3d.png" alt="Spatula" />
            </div>
            <div class="rotating-icon icon-3">
              <img src="/images/salt-3d.png" alt="Salt" />
            </div>
            <div class="rotating-icon icon-4">
              <img src="/images/pepper-3d.png" alt="Pepper" />
            </div>
          </div> -->
      </div>
      <div class="loading-content">
        <!-- Loading Text -->
        <p class="loading-text">Un petit temps de cuisson puis tout est bon !</p>

        <!-- Loading Dots -->
        <div class="loading-dots">
          <div class="dot dot-1">
            <img src="/images/mini-chips-1.png" alt="Mini Chips" />
          </div>
          <div class="dot dot-2">
            <img src="/images/mini-chips-2.png" alt="Mini Chips" />
          </div>
          <div class="dot dot-3">
            <img src="/images/mini-chips-3.png" alt="Mini Chips" />
          </div>
        </div>
      </div>
    </div>

    <!-- Final Chips Result -->
    <div v-if="finalState.isVisible" class="final-overlay" ref="finalOverlay">
      <!-- Fireworks Background Animation -->
      <div v-if="fireworksAnimation" class="fireworks-background">
        <Vue3Lottie
          :animationData="fireworksAnimation"
          :height="100"
          :width="100"
          :loop="true"
          :autoPlay="true"
        />
      </div>
      <!-- Close Button -->
      <button class="close-button" @click="closeFinalState">
        <svg
          width="24"
          height="24"
          viewBox="0 0 24 24"
          fill="none"
          stroke="currentColor"
          stroke-width="2"
        >
          <line x1="18" y1="6" x2="6" y2="18"></line>
          <line x1="6" y1="6" x2="18" y2="18"></line>
        </svg>
      </button>
      <div class="final-content">
        <!-- Chips Container for Sharing/Download -->
        <div class="chips-container" id="chips-container">
          <!-- Top Text -->
          <div class="top-text">Mes Chips !</div>
          <!-- Decorative Potato Images -->
          <div class="decoration-chips left-top">
            <img src="/images/potato.svg" alt="Potato" width="60" height="40" />
          </div>

          <div class="decoration-chips right-top">
            <img src="/images/potato.svg" alt="Potato" width="50" height="35" />
          </div>

          <div class="decoration-chips left-bottom">
            <img src="/images/potato.svg" alt="Potato" width="55" height="38" />
          </div>

          <div class="decoration-chips right-bottom">
            <img src="/images/potato.svg" alt="Potato" width="45" height="32" />
          </div>

          <!-- Quote Images -->
          <div class="motion-lines top-left">
            <img src="/images/quote.svg" alt="Quote" width="40" height="20" />
          </div>

          <div class="motion-lines top-right">
            <img src="/images/quote.svg" alt="Quote" width="40" height="20" />
          </div>

          <div class="motion-lines bottom-left">
            <img src="/images/quote.svg" alt="Quote" width="40" height="20" />
          </div>

          <div class="motion-lines bottom-right">
            <img src="/images/quote.svg" alt="Quote" width="40" height="20" />
          </div>

          <!-- Central Potato Bag -->
          <div class="potato-bag-container">
            <img :src="selectedBagImage" alt="Potato Bag" class="potato-bag" />

            <svg style="display: none">
              <filter id="distort">
                <feTurbulence
                  type="turbulence"
                  baseFrequency="0.005"
                  numOctaves="1"
                  result="turbulence"
                />
                <feDisplacementMap in2="turbulence" in="SourceGraphic" scale="20" />
              </filter>
            </svg>

            <!-- Simplified Text Overlay -->
            <div class="bag-text-overlay">
              <!-- Ingredient Icons -->
              <div class="ingredient-icons">
                <img
                  v-for="(ingredient, index) in selectedIngredients.filter(
                    (ing) => ing.category !== 'chef',
                  )"
                  :key="`icon-${ingredient.id}`"
                  :src="ingredient.icon"
                  :alt="ingredient.name"
                  :class="[
                    'ingredient-icon',
                    { 'cookbook-ingredient': ingredient.category === 'cookbook' },
                  ]"
                  :style="getIngredientIconStyle(index)"
                />
              </div>
              <div class="bag-text-content">
                <img src="/images/logo.png" alt="bag logo" class="bag-logo" />

                <div class="bag-brand">Saveur</div>
                <div class="bag-flavor">
                  <span
                    v-for="(ingredient, index) in selectedIngredients"
                    :key="ingredient.id"
                    :class="`ingredient-${ingredient.category}`"
                    class="ingredient-tag bag-distortion"
                  >
                    {{ ingredient.category === 'chef' ? ingredient.signature : ingredient.name }}
                  </span>
                </div>
                <!-- Chef Info -->
                <div v-if="selectedChef" class="chef-info-overlay">
                  <img :src="selectedChef.icon" :alt="selectedChef.name" class="chef-avatar" />
                  <span class="chef-name-overlay">{{ selectedChef.name }}</span>
                </div>
              </div>
            </div>
          </div>

          <!-- Bottom Text -->
          <div class="bottom-text">Croustillantes et gourmandes</div>
        </div>
      </div>

      <!-- Action Buttons -->
      <div class="final-actions">
        <button class="action-btn download-btn" @click="downloadChipsImage">
          <svg
            width="20"
            height="20"
            viewBox="0 0 24 24"
            fill="none"
            stroke="currentColor"
            stroke-width="2"
          >
            <path d="M21 15v4a2 2 0 0 1-2 2H5a2 2 0 0 1-2-2v-4"></path>
            <polyline points="7,10 12,15 17,10"></polyline>
            <line x1="12" y1="15" x2="12" y2="3"></line>
          </svg>
          Télécharger votre paquet
        </button>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, reactive, computed, onMounted, nextTick } from 'vue'
import { Vue3Lottie } from 'vue3-lottie'
import { gsap } from 'gsap'
import IngredientModal from '../components/IngredientModal.vue'
import IngredientTag from '../components/IngredientTag.vue'
import html2canvas from 'html2canvas'

// Selected ingredients state
const selectedIngredients = ref([])

// Loading overlay ref for GSAP animation
const loadingOverlay = ref(null)

// Final overlay ref for GSAP animation
const finalOverlay = ref(null)

// Dice animation ref
const diceAnimationRef = ref(null)

// Lottie animation data
const smokeAnimation = ref(null)
const diceAnimation = ref(null)
const fireworksAnimation = ref(null)

// Modal state
const modalState = reactive({
  isOpen: false,
  title: '',
  image: '',
  ingredients: [],
})

// Loading state
const loadingState = reactive({
  isLoading: false,
})

// Final state (after cooking is done)
const finalState = reactive({
  isVisible: false,
})

// Computed property for selected chef
const selectedChef = computed(() => {
  return selectedIngredients.value.find((ingredient) => ingredient.category === 'chef')
})

// Computed property for dynamic bag selection
const selectedBagImage = computed(() => {
  if (selectedIngredients.value.length === 0) {
    return '/images/bag-white.png' // Default bag
  }

  // Check if there's a chef selected (chef bags take priority)
  const chefIngredient = selectedIngredients.value.find(
    (ingredient) => ingredient.category === 'chef',
  )
  if (chefIngredient && chefIngredient.bagImage) {
    return chefIngredient.bagImage
  }

  // Count ingredients by category
  const categoryCount = selectedIngredients.value.reduce((acc, ingredient) => {
    acc[ingredient.category] = (acc[ingredient.category] || 0) + 1
    return acc
  }, {})

  // Determine dominant category
  const dominantCategory = Object.keys(categoryCount).reduce((a, b) =>
    categoryCount[a] > categoryCount[b] ? a : b,
  )

  // Map categories to bag colors with 3 variants each
  const bagColorMap = {
    vegetables: ['bag-green-1.png', 'bag-red-2.png', 'bag-green-3.png', 'bag-yellow-1.png'],
    spices: [
      'bag-green-3.png',
      'bag-red-1.png',
      'bag-green-2.png',
      'bag-red-3.png',
      'bag-green-1.png',
    ],
    dairy: ['bag-white-1.png', 'bag-white-2.png', 'bag-white-3.png'],
    meat: ['bag-meat-1.png', 'bag-meat-2.png', 'bag-meat-3.png'],
    sauce: ['bag-yellow-1.png', 'bag-yellow-2.png', 'bag-yellow-3.png'],
    chef: ['bag-white-1.png', 'bag-white-2.png', 'bag-white-3.png'], // fallback for chef category
    cookbook: ['bag-cook.png'], // fallback for chef category
  }

  // Randomly select one of the 3 variants
  const bagVariants = bagColorMap[dominantCategory] || bagColorMap.dairy
  const randomVariantIndex = Math.floor(Math.random() * bagVariants.length)
  const selectedBagVariant = bagVariants[randomVariantIndex]

  return `/images/${selectedBagVariant}`
})

// Ingredient data for each category
const ingredientData = {
  cookbook: {
    title: 'Plats traditionnels',
    image: '/images/cookbook-3d.png',
    items: [
      {
        id: 'cookbook-1',
        name: 'Bœuf bourguignon',
        category: 'cookbook',
        icon: '/images/ingredients/boeuf-bourguignon.png',
      },
      {
        id: 'cookbook-2',
        name: 'Blanquette de veau',
        category: 'cookbook',
        icon: '/images/ingredients/blanquette-de-veau.png',
      },
      {
        id: 'cookbook-3',
        name: 'Cassoulet',
        category: 'cookbook',
        icon: '/images/ingredients/cassoulet.png',
      },
      {
        id: 'cookbook-4',
        name: 'Bouillabaisse',
        category: 'cookbook',
        icon: '/images/ingredients/bouillabaisse.png',
      },
      {
        id: 'cookbook-5',
        name: "Canard à l'orange",
        category: 'cookbook',
        icon: '/images/ingredients/canard-a-l-orange.png',
      },
      {
        id: 'cookbook-6',
        name: 'Fondue savoyarde',
        category: 'cookbook',
        icon: '/images/ingredients/fondue-savoyarde.png',
      },
      {
        id: 'cookbook-7',
        name: 'Quiche lorraine',
        category: 'cookbook',
        icon: '/images/ingredients/quiche-lorraine.png',
      },
      {
        id: 'cookbook-8',
        name: 'Couscous',
        category: 'cookbook',
        icon: '/images/ingredients/couscous.png',
      },
      {
        id: 'cookbook-9',
        name: 'Tajine',
        category: 'cookbook',
        icon: '/images/ingredients/tajine.png',
      },
    ],
  },
  chef: {
    title: 'Chef Signature',
    image: '/images/chef-cook.png',
    items: [
      {
        id: 'chef-1',
        name: 'Philippe Etchebest',
        category: 'chef',
        icon: '/images/chef/etchebest.png',
        signature: 'Raviole de champignons, foie gras poêlé, bouillon crémé',
        bagImage: '/images/chef/bag-etchebest.png',
      },
      {
        id: 'chef-2',
        name: 'Cyril Lignac',
        category: 'chef',
        icon: '/images/chef/lignac.png',
        signature: "Galette d'avocat et crabe au curry Madras",
        bagImage: '/images/chef/bag-lignac.png',
      },
      {
        id: 'chef-3',
        name: 'Paul Bocuse',
        category: 'chef',
        icon: '/images/chef/bocuse.png',
        signature: 'Soupe aux truffes VGE',
        bagImage: '/images/chef/bag-bocuse.png',
      },
      {
        id: 'chef-4',
        name: 'Joël Robuchon',
        category: 'chef',
        icon: '/images/chef/robuchon.png',
        signature: 'Purée de pommes de terre',
        bagImage: '/images/chef/bag-robuchon.png',
      },
    ],
  },
  vegetables: {
    title: 'Légumes',
    image: '/images/vegetables-icon.png',
    items: [
      {
        id: 'veg-1',
        name: 'Tomates',
        category: 'vegetables',
        icon: '/images/ingredients/tomato.png',
      },
      {
        id: 'veg-2',
        name: 'Oignons',
        category: 'vegetables',
        icon: '/images/ingredients/onion.png',
      },
      {
        id: 'veg-3',
        name: 'Poivrons',
        category: 'vegetables',
        icon: '/images/ingredients/bell-pepper.png',
      },
      {
        id: 'veg-4',
        name: 'Courgettes',
        category: 'vegetables',
        icon: '/images/ingredients/zucchini.png',
      },
      {
        id: 'veg-5',
        name: 'Aubergines',
        category: 'vegetables',
        icon: '/images/ingredients/eggplant.png',
      },
      {
        id: 'veg-6',
        name: 'Champignons',
        category: 'vegetables',
        icon: '/images/ingredients/mushroom.png',
      },
      {
        id: 'veg-7',
        name: 'Citron',
        category: 'vegetables',
        icon: '/images/ingredients/lemon.png',
      },
      {
        id: 'veg-8',
        name: 'Orange',
        category: 'vegetables',
        icon: '/images/ingredients/orange.png',
      },
      {
        id: 'veg-9',
        name: 'Ananas',
        category: 'vegetables',
        icon: '/images/ingredients/ananas.png',
      },
    ],
  },
  spices: {
    title: 'Épices',
    image: '/images/spices-icon.png',
    items: [
      { id: 'spice-1', name: 'Cumin', category: 'spices', icon: '/images/ingredients/cumin.png' },
      {
        id: 'spice-2',
        name: 'Paprika',
        category: 'spices',
        icon: '/images/ingredients/paprika.png',
      },
      { id: 'spice-3', name: 'Curry', category: 'spices', icon: '/images/ingredients/curry.png' },
      {
        id: 'spice-4',
        name: 'Coriande',
        category: 'spices',
        icon: '/images/ingredients/coriander.png',
      },
      {
        id: 'spice-5',
        name: 'Origan',
        category: 'spices',
        icon: '/images/ingredients/oregano.png',
      },
      {
        id: 'spice-6',
        name: 'Basilic',
        category: 'spices',
        icon: '/images/ingredients/basilic.png',
      },
      { id: 'spice-7', name: 'Thym', category: 'spices', icon: '/images/ingredients/thyme.png' },
      {
        id: 'spice-8',
        name: 'Piment Espelette',
        category: 'spices',
        icon: '/images/ingredients/espelette-pepper.png',
      },
      { id: 'spice-9', name: 'Ail', category: 'spices', icon: '/images/ingredients/garlic.png' },
      {
        id: 'spice-10',
        name: 'Persil',
        category: 'spices',
        icon: '/images/ingredients/parsley.png',
      },
      {
        id: 'spice-11',
        name: 'Poivre noir',
        category: 'spices',
        icon: '/images/ingredients/black-pepper.png',
      },
      {
        id: 'spice-12',
        name: 'Ciboulette',
        category: 'spices',
        icon: '/images/ingredients/chives.png',
      },
    ],
  },
  dairy: {
    title: 'Produits laitiers',
    image: '/images/dairy-icon.png',
    items: [
      {
        id: 'dairy-1',
        name: 'Fromage râpé',
        category: 'dairy',
        icon: '/images/ingredients/grated-cheese.png',
      },
      {
        id: 'dairy-2',
        name: 'Mozzarella',
        category: 'dairy',
        icon: '/images/ingredients/mozzarella.png',
      },
      {
        id: 'dairy-3',
        name: 'Chèvre',
        category: 'dairy',
        icon: '/images/ingredients/goat-cheese.png',
      },
      {
        id: 'dairy-4',
        name: 'Crème fraîche',
        category: 'dairy',
        icon: '/images/ingredients/cream.png',
      },
      {
        id: 'dairy-5',
        name: 'Parmesan',
        category: 'dairy',
        icon: '/images/ingredients/parmesan.png',
      },
      { id: 'dairy-6', name: 'Beurre', category: 'dairy', icon: '/images/ingredients/butter.png' },
      {
        id: 'dairy-7',
        name: 'Yaourt grec',
        category: 'dairy',
        icon: '/images/ingredients/greek-yogurt.png',
      },
      {
        id: 'dairy-8',
        name: 'Gorgonzola',
        category: 'dairy',
        icon: '/images/ingredients/gorgonzola.png',
      },
      {
        id: 'dairy-9',
        name: 'Roquefort',
        category: 'dairy',
        icon: '/images/ingredients/roquefort.png',
      },
      { id: 'dairy-10', name: 'Comté', category: 'dairy', icon: '/images/ingredients/comte.png' },
      {
        id: 'dairy-11',
        name: 'Cheddar',
        category: 'dairy',
        icon: '/images/ingredients/cheddar.png',
      },
    ],
  },
  meat: {
    title: 'Viandes',
    image: '/images/meat-icon.png',
    items: [
      { id: 'meat-1', name: 'Poulet', category: 'meat', icon: '/images/ingredients/chicken.png' },
      {
        id: 'meat-2',
        name: 'Bœuf haché',
        category: 'meat',
        icon: '/images/ingredients/ground-beef.png',
      },
      { id: 'meat-3', name: 'Porc', category: 'meat', icon: '/images/ingredients/pork.png' },
      {
        id: 'meat-4',
        name: 'Saucisses',
        category: 'meat',
        icon: '/images/ingredients/sausages.png',
      },
      { id: 'meat-5', name: 'Bacon', category: 'meat', icon: '/images/ingredients/bacon.png' },
      { id: 'meat-6', name: 'Jambon', category: 'meat', icon: '/images/ingredients/ham.png' },
      { id: 'meat-7', name: 'Thon', category: 'meat', icon: '/images/ingredients/tuna.png' },
      { id: 'meat-8', name: 'Canard', category: 'meat', icon: '/images/ingredients/duck.png' },
      { id: 'meat-9', name: 'Caviar', category: 'meat', icon: '/images/ingredients/caviar.png' },
      {
        id: 'meat-10',
        name: 'Foie gras',
        category: 'meat',
        icon: '/images/ingredients/foie-gras.png',
      },
      {
        id: 'meat-11',
        name: 'Crevettes',
        category: 'meat',
        icon: '/images/ingredients/shrimp.png',
      },
      { id: 'meat-12', name: 'Kebab', category: 'meat', icon: '/images/ingredients/kebab.png' },
      {
        id: 'meat-13',
        name: 'Saucisson',
        category: 'meat',
        icon: '/images/ingredients/saucisson.png',
      },
      { id: 'meat-14', name: 'Saumon', category: 'meat', icon: '/images/ingredients/salmon.png' },
    ],
  },
  sauce: {
    title: 'Sauces',
    image: '/images/sauce-icon.png',
    items: [
      {
        id: 'sauce-1',
        name: 'Sauce Algérienne',
        category: 'sauce',
        icon: '/images/ingredients/algerian-sauce.png',
      },
      {
        id: 'sauce-2',
        name: 'Sauce BBQ',
        category: 'sauce',
        icon: '/images/ingredients/bbq-sauce.png',
      },
      {
        id: 'sauce-3',
        name: 'Sauce soja',
        category: 'sauce',
        icon: '/images/ingredients/soy-sauce.png',
      },
      {
        id: 'sauce-4',
        name: "Huile d'olive",
        category: 'sauce',
        icon: '/images/ingredients/olive-oil.png',
      },
      {
        id: 'sauce-5',
        name: 'Vinaigre balsamique',
        category: 'sauce',
        icon: '/images/ingredients/balsamic-vinegar.png',
      },
      {
        id: 'sauce-6',
        name: 'Mayonnaise',
        category: 'sauce',
        icon: '/images/ingredients/mayonnaise.png',
      },
      {
        id: 'sauce-7',
        name: 'Ketchup',
        category: 'sauce',
        icon: '/images/ingredients/ketchup.png',
      },
      { id: 'sauce-8', name: 'Miel', category: 'sauce', icon: '/images/ingredients/honey.png' },
      {
        id: 'sauce-9',
        name: 'Caramel',
        category: 'sauce',
        icon: '/images/ingredients/caramel.png',
      },
      {
        id: 'sauce-10',
        name: 'Tabasco',
        category: 'sauce',
        icon: '/images/ingredients/tabasco.png',
      },
      {
        id: 'sauce-11',
        name: 'Harissa',
        category: 'sauce',
        icon: '/images/ingredients/harissa.png',
      },
      {
        id: 'sauce-12',
        name: 'Moutarde',
        category: 'sauce',
        icon: '/images/ingredients/mustard.png',
      },
      { id: 'sauce-13', name: 'Pesto', category: 'sauce', icon: '/images/ingredients/pesto.png' },
      {
        id: 'sauce-14',
        name: 'Hollandaise',
        category: 'sauce',
        icon: '/images/ingredients/hollandaise.png',
      },
      {
        id: 'sauce-15',
        name: 'Houmous',
        category: 'sauce',
        icon: '/images/ingredients/houmous.png',
      },
    ],
  },
}

// Load Lottie animation on component mount
onMounted(async () => {
  try {
    const smokeResponse = await fetch('/images/smoke.json')
    smokeAnimation.value = await smokeResponse.json()

    const diceResponse = await fetch('/images/dice.json')
    diceAnimation.value = await diceResponse.json()

    const fireworksResponse = await fetch('/images/fireworks.json')
    fireworksAnimation.value = await fireworksResponse.json()
  } catch (error) {
    console.error('Error loading Lottie animation:', error)
  }
})

// Methods
const playDiceAnimation = () => {
  // Play dice animation
  if (diceAnimationRef.value) {
    diceAnimationRef.value.play()
  }
}

const randomCombination = () => {
  // Play dice animation
  playDiceAnimation()

  // Clear current selection
  selectedIngredients.value = []

  // Get random ingredients from each category (excluding chef and cookbook)
  Object.keys(ingredientData).forEach((category) => {
    if (category !== 'chef' && category !== 'cookbook') {
      const items = ingredientData[category].items
      if (items.length > 0) {
        const randomIndex = Math.floor(Math.random() * items.length)
        const randomIngredient = items[randomIndex]
        selectedIngredients.value.push(randomIngredient)
      }
    }
  })
}

const openModal = (type) => {
  const data = ingredientData[type]
  if (data) {
    modalState.isOpen = true
    modalState.title = data.title
    modalState.image = data.image
    modalState.ingredients = data.items
  }
}

const closeModal = () => {
  modalState.isOpen = false
  modalState.title = ''
  modalState.image = ''
  modalState.ingredients = []
}

const selectIngredient = (ingredient) => {
  // Check if ingredient is already selected
  const isAlreadySelected = selectedIngredients.value.some((item) => item.id === ingredient.id)

  if (!isAlreadySelected) {
    // For cookbook category, only allow one selection (replace previous cookbook selection)
    if (ingredient.category === 'cookbook') {
      selectedIngredients.value = selectedIngredients.value.filter(
        (item) => item.category !== 'cookbook',
      )
      selectedIngredients.value.push(ingredient)
    } else {
      // For other categories, allow multiple selections
      selectedIngredients.value.push(ingredient)
    }
  }
}

const removeIngredient = (ingredientId) => {
  selectedIngredients.value = selectedIngredients.value.filter((item) => item.id !== ingredientId)
}

const closeFinalState = () => {
  finalState.isVisible = false
  loadingState.isLoading = false
  selectedIngredients.value = []
}

const getIngredientIconStyle = (index) => {
  // Define positions around the bag for ingredient icons
  const positions = [
    { top: '20%', left: '15%', transform: 'rotate(-15deg)' },
    { top: '25%', right: '20%', transform: 'rotate(10deg)' },
    { top: '40%', left: '10%', transform: 'rotate(25deg)' },
    { top: '45%', right: '15%', transform: 'rotate(-20deg)' },
    { top: '60%', left: '20%', transform: 'rotate(15deg)' },
    { top: '65%', right: '25%', transform: 'rotate(-10deg)' },
    { top: '75%', left: '25%', transform: 'rotate(-25deg)' },
    { top: '80%', right: '20%', transform: 'rotate(20deg)' },
  ]

  return positions[index % positions.length]
}

const downloadChipsImage = async () => {
  const chipsContainer = document.getElementById('chips-container')
  if (chipsContainer) {
    try {
      // Configure html2canvas options for better quality
      const canvas = await html2canvas(chipsContainer, {
        backgroundColor: '#ffffff',
        scale: 2, // Higher resolution
        useCORS: true,
        allowTaint: true,
        width: chipsContainer.offsetWidth,
        height: chipsContainer.offsetHeight,
        scrollX: 0,
        scrollY: 0,
      })

      // Convert canvas to blob
      canvas.toBlob(
        (blob) => {
          if (blob) {
            // Create download link
            const url = URL.createObjectURL(blob)
            const link = document.createElement('a')
            link.href = url

            // Generate filename with ingredients
            const ingredientNames = selectedIngredients.value.map((ing) => ing.name).join('-')
            link.download = `mes-chips-${ingredientNames.toLowerCase().replace(/\s+/g, '-')}.png`

            // Trigger download
            document.body.appendChild(link)
            link.click()
            document.body.removeChild(link)

            // Clean up
            URL.revokeObjectURL(url)
          }
        },
        'image/png',
        1.0,
      )
    } catch (error) {
      console.error('Error generating image:', error)
      alert("Erreur lors de la génération de l'image. Veuillez réessayer.")
    }
  }
}

const startCooking = () => {
  // Show loading state
  loadingState.isLoading = true

  // Wait for next tick to ensure the DOM is updated
  nextTick(() => {
    if (loadingOverlay.value) {
      // Set initial state - small circle
      gsap.set(loadingOverlay.value, {
        clipPath: 'circle(0% at 50% 50%)',
      })

      // Animate to large circle that covers everything
      gsap.to(loadingOverlay.value, {
        clipPath: 'circle(100% at 50% 50%)',
        duration: 1.2,
        ease: 'power2.out',
      })
    }
  })

  // Hide loading state and show final state after 5.2 seconds
  setTimeout(() => {
    loadingState.isLoading = true
    finalState.isVisible = true

    // Wait for next tick to ensure the DOM is updated for final overlay
    nextTick(() => {
      console.log(finalOverlay)
      if (finalOverlay) {
        // Set initial state - small circle
        gsap.set(finalOverlay.value, {
          clipPath: 'circle(0% at 50% 50%)',
        })
        gsap.set(finalOverlay.value.lastChild, {
          opacity: 0,
          scale: 0.8,
          y: 10,
        })

        // Animate to large circle that covers everything
        gsap.to(finalOverlay.value, {
          clipPath: 'circle(100% at 50% 50%)',
          duration: 1.2,
          ease: 'power2.out',
        })
        gsap.to(finalOverlay.value.lastChild, {
          opacity: 1,
          scale: 1,
          y: 0,
          delay: 0.4,
        })
      }
    })
  }, 4000)
}
</script>

<style scoped lang="scss">
.home-container {
  display: flex;
  flex-direction: column;
  background: #fbdb93;
  position: relative;
  min-height: 100svh;
}
.main-content {
  flex: 1;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  padding: 2rem;
}

.pan-container {
  position: relative;
  max-height: 380px;
  overflow: hidden;
}

.cooking-pan {
  width: 100%;
  height: auto;
  filter: drop-shadow(0 10px 20px rgba(0, 0, 0, 0.1));
  transition: transform 0.3s ease;
}

.cooking-pan:hover {
  transform: scale(1.05);
}

.cooking-pan.wiggle {
  animation: panWiggle 4s ease-in-out infinite;
}

@keyframes panWiggle {
  0% {
    transform: translateX(0) translateY(0) rotate(0deg);
  }
  10% {
    transform: translateX(-0.5px) translateY(-1px) rotate(-0.5deg);
  }
  20% {
    transform: translateX(0.5px) translateY(1px) rotate(0.5deg);
  }
  30% {
    transform: translateX(-1px) translateY(-0.5px) rotate(-0.3deg);
  }
  40% {
    transform: translateX(1px) translateY(0.5px) rotate(0.3deg);
  }
  50% {
    transform: translateX(-0.5px) translateY(0px) rotate(-0.2deg);
  }
  60% {
    transform: translateX(0.5px) translateY(-1px) rotate(0.2deg);
  }
  70% {
    transform: translateX(-1px) translateY(1px) rotate(-0.1deg);
  }
  80% {
    transform: translateX(1px) translateY(-1px) rotate(0.1deg);
  }
  90% {
    transform: translateX(-1px) translateY(0px) rotate(-0.05deg);
  }
  100% {
    transform: translateX(0) translateY(0) rotate(0deg);
  }
}

.smoke-animation {
  position: absolute;
  top: 10%;
  left: 50%;
  transform: translateX(-50%) scale(1.5);
  pointer-events: none;
  z-index: 10;
}

.ingredients-in-pan {
  display: flex;
  flex-wrap: wrap;
  justify-content: center;
  align-items: center;
  gap: 8px;
  margin-bottom: 2rem;
  position: absolute;
  top: 55%;
  background: #fbdb93;
  width: 100%;
}

.ingredients-in-pan :deep(.ingredient-tag) {
  background: white;
  color: var(--purple);
  border: 1px solid #e0e0e0;
  border-radius: 20px;
  padding: 0.5rem 1rem;
  font-size: 0.9rem;
  font-weight: 500;
}

.instruction-text {
  font-family: 'Dekko';
  font-weight: normal;
  font-style: normal;
  font-size: 1.2rem;
  color: #000;
  font-weight: 500;
  text-align: center;
  margin-bottom: 2rem;
}

.dock-container {
  display: flex;
  align-items: center;
  gap: 16px;
}

.dock-item {
  width: 80px;
  height: 80px;
  display: flex;
  align-items: center;
  justify-content: center;
  cursor: pointer;
  transition: transform 0.2s ease;
  border-radius: 12px;
}

.dock-icon {
  width: 100%;
  height: 100%;
  object-fit: contain;
  transition: all 0.3s linear;
}
.dock-icon:hover {
  transform: scale(1.1);
}
.chef-cook-icon {
  width: 100%;
  height: 100%;
}

.cookbook-icon {
  width: 100%;
  height: 100%;
}

/* Disabled dock items */
.dock-item.disabled {
  opacity: 0.5;
  cursor: not-allowed;
  position: relative;
}

.dock-item.disabled:hover {
  transform: none;
}

.dock-item.disabled .dock-icon:hover {
  transform: none;
}

/* Tooltip styles */
.dock-item {
  position: relative;
}

.tooltip {
  visibility: hidden;
  opacity: 0;
  position: absolute;
  bottom: 100%;
  left: 50%;
  transform: translateX(-50%);
  background-color: rgba(0, 0, 0, 0.8);
  color: white;
  text-align: center;
  padding: 0.5rem 0.75rem;
  border-radius: 6px;
  font-size: 0.8rem;
  font-weight: 500;
  white-space: nowrap;
  z-index: 1000;
  margin-bottom: 0.5rem;
  transition:
    opacity 0.3s ease,
    visibility 0.3s ease;
}

.tooltip::after {
  content: '';
  position: absolute;
  top: 100%;
  left: 50%;
  transform: translateX(-50%);
  border: 5px solid transparent;
  border-top-color: rgba(0, 0, 0, 0.8);
}

.dock-item.disabled:hover .tooltip {
  visibility: visible;
  opacity: 1;
}

.bottom-app-bar {
  padding-bottom: 16px;
  border-bottom: solid 1px rgba(0, 0, 0, 0.1);
}

.bottom-action {
  padding: 1rem;
  display: flex;
  justify-content: center;
  align-items: center;
  gap: 1rem;
}

.dice-lottie-container {
  cursor: pointer;
  transition: transform 0.2s ease;
  border-radius: 12px;
  display: flex;
  align-items: center;
  justify-content: center;
}

.dice-lottie-container:hover {
  transform: scale(1.1);
  filter: brightness(1.1);
}

.action-button {
  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: 0.5rem;
  background: var(--purple);
  color: white;
  border: none;
  border-radius: 100px;
  padding: 0.75rem 2rem;
  font-size: 1rem;
  font-weight: 600;
  cursor: pointer;
  transition: all 0.3s ease;
  box-shadow: 0 4px 12px rgba(44, 62, 80, 0.3);
  max-height: 64px;
}

.action-button:hover {
  transform: scale(1.05);
  box-shadow: 0 6px 20px rgba(44, 62, 80, 0.4);
}
.action-button:hover .action-icon {
  height: 100px;
  transform: translateX(-13px) rotate(-13deg);
}

.action-icon {
  width: auto;
  height: 100px;
  transform: translateX(-15px) rotate(-15deg);
  transition: all 0.3s linear;
}

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

/* Loading Screen Styles */
.loading-overlay {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100svh;
  background: #f7f3df;
  background: radial-gradient(circle, rgba(247, 243, 223, 1) 0%, rgba(251, 219, 147, 1) 100%);
  display: flex;
  align-items: center;
  justify-content: center;
  z-index: 2000;
}

.loading-content {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  text-align: center;
  z-index: 1;
}

/* Central Chips Container */
.central-chips {
  margin-bottom: 3rem;
}

.chips-image {
  width: 40%;
  object-fit: fill;
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  animation: rotateChips 250s forwards infinite;
}

@keyframes rotateChips {
  0% {
    transform: translate(-50%, -50%) rotate(0deg);
  }
  100% {
    transform: translate(-50%, -50%) rotate(3600deg);
  }
}

/* Rotating Icons */
.rotating-icons {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
}

.rotating-icon {
  position: absolute;
  width: 100px;
  height: 100px;
}

.rotating-icon img {
  width: 100%;
  height: 100%;
  object-fit: contain;
}

.icon-1 {
  top: 0;
  left: 0;
}

.icon-2 {
  top: 0;
  right: 0;
}

.icon-3 {
  bottom: 0;
  left: 0;
}

.icon-4 {
  bottom: 0;
  right: 0;
}

/* Loading Text */
.loading-text {
  font-family: 'Dekko';
  font-size: 1.4rem;
  color: var(--purple);
  font-weight: 500;
  margin-bottom: 2rem;
  line-height: 1.4;
  white-space: nowrap;
}

/* Loading Dots */
.loading-dots {
  display: flex;
  gap: 1rem;
  align-items: center;
}

.dot {
  width: 48px;
  height: 48px;
  opacity: 0.3;
  animation: loadingDots 1.5s ease-in-out infinite;
}

.dot img {
  width: 100%;
  height: 100%;
  object-fit: contain;
}

.dot-1 {
  animation-delay: 0s;
}

.dot-2 {
  animation-delay: 0.3s;
}

.dot-3 {
  animation-delay: 0.6s;
}

@keyframes loadingDots {
  0%,
  60%,
  100% {
    opacity: 0.3;
    transform: scale(1);
  }
  30% {
    opacity: 1;
    transform: scale(1.2);
  }
}

/* Final Chips Result Styles */
.final-overlay {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100svh;
  background: radial-gradient(circle, rgba(247, 243, 223, 1) 0%, rgba(251, 219, 147, 1) 100%);
  display: flex;
  align-items: center;
  justify-content: center;
  z-index: 2000;
  animation: finalFadeIn 0.8s ease-out;
}

.fireworks-background {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  width: 80%;
  height: 100%;
  z-index: 1;
  pointer-events: none;

  > div {
    width: 100% !important;
    height: 100% !important;
  }
}

@keyframes finalFadeIn {
  from {
    opacity: 0;
    transform: scale(0.95);
  }
  to {
    opacity: 1;
    transform: scale(1);
  }
}

.final-content {
  position: relative;
  width: 100%;
  max-width: 600px;
  padding: 2rem;
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 2rem;
  z-index: 2;
}

.close-button {
  position: absolute;
  top: 1rem;
  right: 1rem;
  width: 48px;
  height: 48px;
  border-radius: 50%;
  background: rgba(255, 255, 255, 0.9);
  border: none;
  cursor: pointer;
  display: flex;
  align-items: center;
  justify-content: center;
  color: #666;
  transition: all 0.3s ease;
  z-index: 10;
}

.close-button:hover {
  background: rgba(255, 255, 255, 1);
  transform: scale(1.1);
  color: #333;
}

.chips-container {
  position: relative;
  width: 500px;
  height: 600px;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  overflow: hidden;
}

/* Decorative Chips Positioning */
.decoration-chips {
  position: absolute;
  animation: float 6s ease-in-out infinite;
}

.decoration-chips.left-top {
  top: 10%;
  left: 5%;
  animation-delay: 0s;
}

.decoration-chips.right-top {
  top: 15%;
  right: 8%;
  animation-delay: 1.5s;
}

.decoration-chips.left-bottom {
  bottom: 15%;
  left: 8%;
  animation-delay: 3s;
}

.decoration-chips.right-bottom {
  bottom: 20%;
  right: 5%;
  animation-delay: 4.5s;
}

@keyframes float {
  0%,
  100% {
    transform: translateY(0px) rotate(0deg);
  }
  50% {
    transform: translateY(-10px) rotate(2deg);
  }
}

/* Motion Lines Positioning */
.motion-lines {
  position: absolute;
  opacity: 0.6;
}

.motion-lines.top-left {
  top: 8%;
  left: 15%;
  animation-delay: 0s;
}

.motion-lines.top-right {
  top: 8%;
  right: 9%;
  animation-delay: 1s;
  transform: rotateY(180deg);
}

.motion-lines.bottom-left {
  bottom: 10%;
  left: 6%;
  animation-delay: 2s;
  transform: rotateX(180deg);
}

.motion-lines.bottom-right {
  bottom: 10%;
  right: 5%;
  animation-delay: 3s;
  transform: rotateX(180deg) rotateY(180deg);
}

/* Central Potato Bag */
.potato-bag-container {
  position: relative;
  z-index: 5;
  display: flex;
  align-items: center;
  justify-content: center;
}

.potato-bag {
  width: 80%;
  object-fit: contain;
}

/* Simplified Text Overlay */
.bag-text-overlay {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  display: flex;
  align-items: center;
  justify-content: center;
  height: 80%;
  background: radial-gradient(circle, rgba(255, 255, 255, 0.3) 20%, rgba(251, 219, 147, 0) 40%);
}

.bag-text-content {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: flex-start;
  text-align: center;
  padding: 0rem 0.5rem 1rem 1.75rem;
  position: relative;
  z-index: 1;
  height: 100%;
}
.bag-logo {
  width: 120px;
  margin-left: 12px;
}
.bag-brand {
  font-family: 'Dekko';
  color: var(--purple);
  font-size: 1.2rem;
  font-weight: normal;
  letter-spacing: 1px;
  margin-bottom: 0.5rem;
}

.bag-flavor {
  max-width: 210px;
  word-wrap: break-word;
  margin-bottom: 1rem;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  flex-wrap: wrap;

  .ingredient-tag {
    font-family: 'Dela Gothic One', 'Arial Black', sans-serif;
    font-size: 2.3rem;
    font-weight: 900;
    line-height: 1.2;
    text-align: center;
    color: white;
    text-shadow: 1px 1px 6px rgba(0, 0, 0, 0.2);
    mix-blend-mode: difference;
  }
  .ingredient-chef {
    font-family: 'Quintessential', serif;
    white-space: normal;
    font-size: 1.8rem;
    text-shadow: 1px 1px 6px rgba($color: #ceba0e, $alpha: 0.2);
  }
  .ingredient-cookbook {
    font-family: 'Quintessential', serif;
    white-space: normal;
    font-size: 1.8rem;
    text-shadow: 1px 1px 6px rgba($color: #ceba0e, $alpha: 0.2);
  }

  .ingredient-vegetables {
    font-family: 'Oregano', cursive;
    // color: #b0db9c;
  }

  .ingredient-spices {
    font-family: 'Indie Flower', cursive;
    // color: #ff8282;
  }

  .ingredient-dairy {
    font-family: 'Oregano', cursive;
    // color: #ffc785;
  }

  .ingredient-meat {
    font-family: 'Oregano', cursive;
    // color: #e78f81;
  }

  .ingredient-sauce {
    font-family: 'Indie Flower', cursive;
    // color: #ff6500;
  }
}

/* Bag distortion effect that works with html2canvas */
.bag-distortion {
  transform: perspective(1000px) rotateX(5deg) skew(-2deg, 1deg);
  transform-origin: center;
}

/* Chef Info Overlay */
.chef-info-overlay {
  display: flex;
  align-items: center;
  gap: 8px;
  padding: 8px 12px;
  z-index: 10;
}

.chef-avatar {
  width: 40px;
  height: 40px;
  border-radius: 50%;
  object-fit: cover;
  border: solid 2px white;
}

.chef-name-overlay {
  font-family: 'Dekko', sans-serif;
  font-size: 1.4rem;
  color: white;
  font-weight: bold;
  white-space: nowrap;
}

/* Ingredient Icons */
.ingredient-icons {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  pointer-events: none;
  display: flex;
  width: 230px;
  align-items: center;
  justify-content: center;
  flex-wrap: wrap;
  gap: 0.2px;
}

.ingredient-icon {
  width: 60px;
  object-fit: contain;
  opacity: 0.8;
}

.ingredient-icon.cookbook-ingredient {
  width: 140px;
}
/* Top Text */
.top-text {
  position: absolute;
  top: 3%;
  left: 50%;
  transform: translateX(-50%);
  font-family: 'Dekko', sans-serif;
  color: var(--purple);
  font-size: 1.5rem;
  text-align: center;
  width: 100%;
  white-space: nowrap;
  letter-spacing: 0.5px;
  word-spacing: 2px;
  font-weight: 400;
  line-height: 1.2;
}

/* Bottom Text */
.bottom-text {
  position: absolute;
  bottom: 3%;
  left: 50%;
  transform: translateX(-50%);
  font-family: 'Dekko', sans-serif;
  color: var(--purple);
  font-size: 1.5rem;
  text-align: center;
  width: 100%;
  white-space: nowrap;
  letter-spacing: 0.5px;
  word-spacing: 2px;
  font-weight: 400;
  line-height: 1.2;
}

/* Action Buttons */
.final-actions {
  display: flex;
  gap: 1rem;
  margin-top: 1rem;
  position: absolute;
  bottom: 3rem;
  z-index: 10;
}

.action-btn {
  display: flex;
  align-items: center;
  gap: 0.5rem;
  padding: 1rem 1.75rem;
  border: none;
  border-radius: 25px;
  font-size: 1rem;
  font-weight: 600;
  cursor: pointer;
  transition: all 0.3s ease;
}

.download-btn {
  background: #ffffff;
  color: var(--purple);
}

.download-btn:hover {
  background: #f8f9fa;
  transform: translateY(-2px);
  box-shadow: 0 6px 20px rgba(0, 0, 0, 0.15);
}
</style>
