<template>
  <div class="container">
    <h1 class="sr-only">Peek a Vue</h1>
    <img src="@/assets/images/peek-a-vue-title.png" alt="title game" class="title">
    <transition-group tag="section" name="shuffle-card" class="game-board">
      <Card v-for="card in cardList" 
            :key="`${card.value}-${card.variant}`" 
            :value="card.value" 
            :visible="card.visible"
            :position="card.position"
            :matched="card.matched"
            @select-card="flipCard"/> 
    </transition-group>
    <h2>{{ status }}</h2>
    <button @click="restartGame" class="button"> 
      <img src="@/assets/images/restart.svg" alt="restart button"> 
      Restart Game
    </button>
  </div>
</template>

<script>
import _ from 'lodash'
import Card from '@/components/Card.vue';
import { computed, ref } from '@vue/reactivity';
import { watch } from '@vue/runtime-core';
import { lauchConfetti } from '@/utilities/confetti'

export default {
  name: 'App',
  components: {
    Card
  },
  setup(){
    const cardList = ref([])
    const userSelection = ref([])

    // player status in game win or keep playing
    const status = computed(() => {
      if(remainingPairs.value === 0){
        return 'Player Wins!!'
      }else{
        return `Remaining Paris: ${remainingPairs.value}`
      }
    })

    //Predict the cards ramaining
    const remainingPairs = computed(() => {
      const remainingCards = cardList.value.filter((card) => card.matched === false).length
      console.log(remainingCards)
      return remainingCards / 2
    })

    //restart game and shuffle
    const restartGame = () => {
      cardList.value = _.shuffle(cardList.value)

      cardList.value = cardList.value.map((card, index) => {
        return{
          ...card,
          matched: false,
          position: index,
          visible: false
        }
      })
    }

    // create a cople cards for find a metch
    const cardItems = ['bat', 'candy', 'cauldron', 'cupcake', 'ghost', 'moon', 'pumpkin', 'witch-hat']

    cardItems.forEach( item  => {
      cardList.value.push({
        value: item,
        variant: 1,
        visible: false  ,
        position: null,
        matched: false,
      })
      cardList.value.push({
        value: item,
        variant: 2,
        visible: false,
        position: null,
        matched: false,
      })   

    })
     cardList.value = cardList.value.map((card, index) => {
       return{
         ...card,
         position: index,

       }
     })


    // Flipe cards and check the value
    const flipCard = (payload) => {
      cardList.value[payload.position].visible = true

      if(userSelection.value[0]){
        if (userSelection.value[0].position === payload.position && userSelection.value[0].faceValue === payload.faceValue){
          return

        } else {
          userSelection.value[1] = payload
        }
      }else {
        userSelection.value[0] = payload
      }
    }

    //Lauch confetti animation on win game
    watch(remainingPairs, (currentValue) => {
      if(currentValue === 0){
        lauchConfetti()
      }
    })

    // Flipe cards and check the value
    watch(userSelection, (currentValue) => { 
      if (currentValue.length === 2){ 
        const cardOne =  currentValue[0]
        const cardTwo =  currentValue[1]

        if(cardOne.faceValue === cardTwo.faceValue){
          cardList.value[cardOne.position].matched = true
          cardList.value[cardTwo.position].matched = true
        }else {
          setTimeout(() => {
            cardList.value[cardOne.position].visible = false
            cardList.value[cardTwo.position].visible = false
          }, 1000)
        }
        userSelection.value.length = 0
      }
    }, {  deep: true })
    return{
      cardList, flipCard, userSelection,status, restartGame
    }
  }
}
</script>

<style>

html, body{
  margin: 0;
  padding: 0; 
}
h1{
  margin-top: 0;
}
.container{
  display: flex;
  height:100vh;
  flex-direction: column;
  align-items: center;
  justify-content: center;

}
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  background-image: url('./assets/images/page-bg.png');
  background-color: #00070c;
  height:100vh;
  color: #fff;
  
}
.title{
  padding-bottom: 30px;
}


.game-board{
  display: grid;
  grid-template-columns: repeat(4, 120px);
  grid-template-rows: repeat(4, 120px);
  grid-row-gap: 30px;
  grid-column-gap: 30px;
  justify-content: center;
}
.sr-only{
  position: absolute;
  width: 1px;
  height: 1px;
  padding: 0;
  margin: -1px;
  overflow: hidden;
  clip: rect(0,0,0,0);
  border: 0;

}
button{
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 5px;

  background-color: orange;
  font-weight: 700;
  color: white;
  padding: 1rem;

  border-radius: 10px;
  border: none;
  outline: none;

  cursor: pointer;
  transition: all ease 0.2s;
}
button:hover{
  filter: brightness(0.8);
}

.shuffle-card-move{
  transition: transform 0.6s ease-in;
}
</style>
