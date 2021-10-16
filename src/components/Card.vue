<template>
    <div class="card" :class="flippedStyles" @click="selectCard">
        <div  class="card-face is-front">
            <img :src="require(`@/assets/images/${value}.png`)" :alt="value" />
            <img v-if="matched" src="../assets/images/checkmark.svg" class="icon-checkmark"/>
        </div>
        <div class="card-face is-back">
        </div>
    </div>
</template>

<script>
import { computed } from '@vue/reactivity'

    export default {
        props: {
            matched: {
                type: Boolean,
                default: false
            },
            value: {
                type: String,
                required: true,
            },
            visible:{
                type: Boolean,
                default: false,
            },
            position:{
                type: Number,
                required: true 
            }
        },
        setup(props, context){
            const flippedStyles = computed(() => {
                if(props.visible){
                    return 'is-flipped'
                }
            })

            const selectCard = () => {
                context.emit('select-card', { 
                    position: props.position,
                    faceValue: props.value

                })
            }
            return{
                flippedStyles,
                selectCard
            }
        }
    }
</script>

<style lang="css" scoped>
.card{
  position: relative;
  transition: 0.5s transform ease;
  transform-style: preserve-3d;
}
.card.is-flipped{
    transform: rotateY(180deg);

}
.card-face{
    width: 100%;
    height: 100%;
    position: absolute;
    border-radius: 10px;
    display: flex;
    align-items: center;
    justify-content: center;
    backface-visibility: hidden;
    
}
.card-face.is-front{
    background-image: url('../assets/images/card-bg.png');
    color: white;
    transform: rotateY(180deg);
}
.card-face.is-back{
    background-image: url('../assets/images/card-bg-empty.png');
    color: white;
}
.icon-checkmark{
    position: absolute;
    right: 5px;
    bottom: 5px;
}

</style>