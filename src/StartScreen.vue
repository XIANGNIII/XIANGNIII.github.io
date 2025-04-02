<template>
    <div class="start-screen">
        <div class="game-frame">

            <template v-if="!showInstructions">
                <div class="title-container">
                    <h1 class="game-title">cooKING</h1>
                    <div class="title-shadow"></div>
                </div>

                <div class="buttons-container">
                    <button class="start-button" @click="startGame">
                        <span class="button-text">START GAME</span>
                    </button>

                    <button class="instructions-button" @click="toggleInstructions">
                        <span class="button-text">HOW TO PLAY</span>
                    </button>
                </div>

                <div class="startChef"><img src="@/assets/pic/右边静止.png" alt="chef" /></div>
            </template>


            <template v-else>
                <div class="instructions-container">
                    <h2 class="instructions-title">HOW TO PLAY</h2>

                    <div class="instructions-content">
                        <div class="instruction-section">

                            <h3> Discover and create 9 different delicious dishes, and become the cooKING!</h3>
                            <ul>
                                <li> <span class="pixel-highlight">Click and drag</span> ingredients into the selected
                                    ingredients area.</li>
                                <li>You can choose up to <span class="pixel-highlight">5</span> ingredients each time
                                    you cook.</li>
                                <li>You need to select at least <span class="pixel-highlight">2</span> ingredients to
                                    start cooking.</li>
                                <li>Click the <span class="pixel-highlight">"Start Cooking"</span> button and wait for
                                    the cooking to finish.</li>
                            </ul>




                            <button class="return-button" @click="toggleInstructions">
                                <span class="button-text">RETURN</span>
                            </button>
                        </div>
                    </div>
                </div>
            </template>
        </div>
    </div>
</template>

<script>
import '@/assets/startScreen.css';
export default {
    name: 'StartScreen',


    emits: ['game-start'],


    data() {
        return {
            showInstructions: false
        };
    },


    methods: {
        startGame() {
            this.$emit('game-start');
        },

        toggleInstructions() {
            this.showInstructions = !this.showInstructions;
        }
    }
};
</script>
