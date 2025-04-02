<template>
  <template v-if="!gameStarted">
    <StartScreen @game-start="startGame" />
  </template>
  <template v-else>
    <div class="game-container">
      <div class="ingredients-bar">
        <h2>Ingredients Bar</h2>
        <div class="ingredients-list">
          <div v-for="ingredient in ingredients" :key="ingredient.id" class="ingredient-item" draggable="true"
            @dragstart="startDrag($event, ingredient)">
            <img :src="ingredient.image" :alt="ingredient.name" class="pixel-art">
            <div class="ingredient-name">{{ ingredient.name }}</div>
          </div>
        </div>
      </div>


      <div class="game-main-area">

        <div class="game-left-side">
          <div class="selected-ingredients" @dragover.prevent @drop="onDrop">
            <h2>Selected Ingredients</h2>
            <div class="selected-list">
              <div v-for="(item, index) in selectedIngredients" :key="index" class="selected-item">
                <img :src="item.image" :alt="item.name" class="pixel-art">
                <button class="remove-btn" @click="removeIngredient(index)">×</button>
              </div>
              <div v-for="n in (5 - selectedIngredients.length)" :key="`empty-${n}`" class="empty-slot">
                <div class="placeholder">+</div>
              </div>
            </div>
          </div>
        </div>


        <div class="game-middle-side">

          <div class="cooking-button-container">
            <button :class="['cook-button', { 'disabled': selectedIngredients.length < 2 }]" @click="startCooking"
              :disabled="selectedIngredients.length < 2 || isCooking">
              <span v-if="!isCooking">Start Cooking</span>
              <div v-else class="progress-container">
                <div class="progress-bar" :style="{ width: `${cookingProgress}%` }"></div>

              </div>
            </button>
          </div>

          <div class="completed-dishes">
            <h2>Completed Dishes</h2>
            <div class="dishes-grid">
              <div v-for="(dish, index) in dishes" :key="dish.id" class="dish-card">
                <div class="dish-inner">
                  <img v-if="completedDishes.includes(dish.id)" :src="dish.image" :alt="dish.name" class="pixel-art">
                  <template v-else>
                    <div class="unknown">?</div>
                    <div class="dish-name">{{ dish.name }}</div>
                  </template>
                </div>
              </div>
            </div>
          </div>

        </div>

        <div class="game-right-side">
          <div class="chef">
            <img v-if="!isCooking" src="@/assets/pic/右边静止.png" alt="Chef" class="pixel-art">
            <img v-else src="@/assets/pic/右边cook.gif" alt="movingChef" class="pixel-art">
          </div>
        </div>
      </div>


      <div v-if="showPopup" class="popup-overlay">
        <div class="popup">
          <div v-if="popupType === 'new'" class="fireworks"></div>
          <h3>{{ popupTitle }}</h3>
          <img :src="popupImage" alt="Dish" class="dish-image pixel-art">
          <div class="popup-buttons">
            <button v-if="popupType === 'new'" @click="collectDish" class="collect-btn">Collect</button>
            <button v-if="popupType === 'strange'" @click="closePopup" class="drop-btn">Drop</button>
          </div>
        </div>
      </div>
    </div>

    <div v-if="showCongratulations" class="congratulations-overlay">
      <div class="congratulations-container">
        <div class="confetti-container">

          <div v-for="n in 30" :key="`confetti-${n}`" class="confetti" :style="getRandomConfettiStyle()"></div>
        </div>

        <div class="fireworks-container">

          <div class="firework" v-for="n in 3" :key="`firework-${n}`"></div>
        </div>

        <h2 class="congrats-title">CONGRATULATIONS!</h2>
        <p class="congrats-subtitle">You've become the cooKING!</p>

        <div class="trophy-container">
          <img src="@/assets/pic/奖杯.png" alt="Trophy" class="trophy"
            onerror="this.src='data:image/svg+xml;utf8,<svg xmlns=%22http://www.w3.org/2000/svg%22 width=%22100%22 height=%22100%22 viewBox=%220 0 100 100%22><polygon points=%2250,10 61,35 90,35 65,55 75,80 50,65 25,80 35,55 10,35 39,35%22 fill=%22%23FFD700%22 stroke=%22%23DAA520%22 stroke-width=%222%22/></svg>'">
        </div>

        <p class="stats-text">You discovered all 9 delicious dishes!</p>

        <button class="return-button congrats-return" @click="resetGame">
          <span class="button-text">RETURN TO MENU</span>
        </button>
      </div>
    </div>
  </template>

</template>


<script>
import spicyMeat from '@/assets/pic/香辣炒肉.png';
import tomatoEgg from '@/assets/pic/西红柿炒鸡蛋.png';
import spicyEgg from '@/assets/pic/香辣炒鸡蛋.png';
import borscht from '@/assets/pic/罗宋汤.png';
import mushroomSoup from '@/assets/pic/蘑菇洋葱浓汤.png';
import potatoPancake from '@/assets/pic/土豆馅饼.png';
import strawberryCake from '@/assets/pic/草莓蛋糕.png';
import milkPudding from '@/assets/pic/牛奶布丁.png';
import meatRice from '@/assets/pic/美味肉盖饭.png';
import strangeDish from '@/assets/pic/奇怪的料理.png';
import StartScreen from './StartScreen.vue';
import '@/assets/App.css';
import meatImage from '@/assets/pic/肉.png';
import chiliImage from '@/assets/pic/辣椒.png';
import eggImage from '@/assets/pic/鸡蛋.png';
import tomatoImage from '@/assets/pic/番茄.png';
import milkImage from '@/assets/pic/牛奶.png';
import onionImage from '@/assets/pic/洋葱.png';
import wheatImage from '@/assets/pic/麦子.png';
import potatoImage from '@/assets/pic/土豆.png';
import strawberryImage from '@/assets/pic/草莓.png';
import mushroomImage from '@/assets/pic/蘑菇.png';
      

export default {
  components: {
    StartScreen
  },
  
  data() {
    return {
      ingredients: [
        { id: 1, name: 'Meat', image: meatImage },
        { id: 2, name: 'Chili', image: chiliImage },
        { id: 3, name: 'Egg', image: eggImage },
        { id: 4, name: 'Tomato', image: tomatoImage },
        { id: 5, name: 'Milk', image: milkImage },
        { id: 6, name: 'Onion', image: onionImage },
        { id: 7, name: 'Wheat', image: wheatImage },
        { id: 8, name: 'Potato', image: potatoImage },
        { id: 9, name: 'Strawberry', image: strawberryImage },
        { id: 10, name: 'Mushroom', image: mushroomImage },
      ],

      dishes: [
        { id: 1, name: 'Stir-Fried Tomato and Egg', image: tomatoEgg, ingredients: [3, 4], optionalIngredients: [6, 8, 10] },
        { id: 2, name: 'Creamy Mushroom & Onion Soup', image: mushroomSoup, ingredients: [5, 6, 10], optionalIngredients: [1, 8] },
        { id: 3, name: 'Spicy Stir-Fried Meat', image: spicyMeat, ingredients: [1, 2], optionalIngredients: [3, 4, 6, 8, 10] },
        { id: 4, name: 'Spicy Stir-Fried Egg', image: spicyEgg, ingredients: [2, 3], optionalIngredients: [4, 6, 8, 10] },
        { id: 5, name: 'Milk Pudding', image: milkPudding, ingredients: [3, 5], optionalIngredients: [9] },
        { id: 6, name: 'Potato Pancake', image: potatoPancake, ingredients: [3, 7, 8], optionalIngredients: [5, 10] },
        { id: 7, name: 'Strawberry Cake', image: strawberryCake, ingredients: [3, 5, 7, 9], optionalIngredients: [] },
        { id: 8, name: 'Borscht', image: borscht, ingredients: [1, 4, 6, 8], optionalIngredients: [10] },
        { id: 9, name: 'Delicious Meat Rice Bowl', image: meatRice, ingredients: [1, 7, 10], optionalIngredients: [2, 3, 4, 6, 8] },
      ],

      selectedIngredients: [],
      completedDishes: [],
      isCooking: false,
      cookingProgress: 0,
      showPopup: false,
      popupType: '',
      popupTitle: '',
      popupImage: '',
      currentDishId: null,
      gameStarted: false,
      showCongratulations: false
    };
  },


  methods: {

    startGame() {
      this.gameStarted = true;
    },


    startDrag(event, ingredient) {
      event.dataTransfer.setData('ingredientId', ingredient.id);
    },


    onDrop(event) {
      const ingredientId = parseInt(event.dataTransfer.getData('ingredientId'));
      const ingredient = this.ingredients.find(item => item.id === ingredientId);

      if (this.selectedIngredients.length < 5 && !this.selectedIngredients.some(item => item.id === ingredientId)) {
        this.selectedIngredients.push(ingredient);
      }
    },


    removeIngredient(index) {
      this.selectedIngredients.splice(index, 1);
    },


    startCooking() {
      if (this.selectedIngredients.length < 2 || this.isCooking) return;

      this.isCooking = true;
      this.cookingProgress = 0;

      const progressInterval = setInterval(() => {
        this.cookingProgress += 2;

        if (this.cookingProgress >= 100) {
          clearInterval(progressInterval);
          this.finishCooking();
        }
      }, 25);
    },

    finishCooking() {
      setTimeout(() => {
        this.isCooking = false;

        const selectedIds = this.selectedIngredients.map(item => item.id);

        const cookedDish = this.dishes.find(dish => {
          const requiredIngredients = dish.ingredients.every(id => selectedIds.includes(id));
          const extraIngredients = selectedIds.some(id =>
            !dish.ingredients.includes(id) && !dish.optionalIngredients.includes(id)
          );

          return requiredIngredients && !extraIngredients;
        });

        if (cookedDish) {
          if (this.completedDishes.includes(cookedDish.id)) {
            this.showPopupDialog('repeat', cookedDish.name, cookedDish.image, cookedDish.id);
          } else {
            this.showPopupDialog('new', cookedDish.name, cookedDish.image, cookedDish.id);
          }
        } else {

          this.showPopupDialog('strange', 'Weird Dish', strangeDish, null);
        }
      }, 100);
    },


    showPopupDialog(type, title, image, dishId) {
      this.popupType = type;
      this.popupTitle = title;
      this.popupImage = image;
      this.currentDishId = dishId;
      this.showPopup = true;


      if (type === 'repeat') {
        setTimeout(() => {
          this.closePopup();
        }, 2000);
      }
    },


    collectDish() {
      if (this.currentDishId && !this.completedDishes.includes(this.currentDishId)) {
        this.completedDishes.push(this.currentDishId);
      }

      if (this.completedDishes.length === 9) {
        console.log('congratulations');
        setTimeout(() => {
          this.showCongratulations = true;
        }, 1000);
      }
      this.closePopup();
    },


    closePopup() {
      this.showPopup = false;
      this.selectedIngredients = [];
    },


    resetGame() {
      this.showCongratulations = false;
      this.completedDishes = [];
      this.gameStarted = false;
    },


    getRandomConfettiStyle() {
      const colors = ['#FFD700', '#FF6347', '#4CAF50', '#4A9FF5', '#E91E63', '#9C27B0'];
      return {
        '--fall-duration': `${Math.random() * 3 + 2}s`,
        '--fall-delay': `${Math.random() * 2}s`,
        background: colors[Math.floor(Math.random() * colors.length)],
        left: `${Math.random() * 100}%`,
        width: `${Math.random() * 10 + 5}px`,
        height: `${Math.random() * 10 + 5}px`,
        transform: `rotate(${Math.random() * 360}deg)`
      };
    }
  }
}
</script>
