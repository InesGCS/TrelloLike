<template>
<div id="cardManager">
<h2>I'm the card Manager !</h2>
<button class='addCardButton' v-if="addCardForm === false" @click="changeAddCardForm" >+ Add another card</button>
  <form v-else @submit.prevent>
      <input class='cardContent' v-model="newContent" placeholder='Enter new content...' />
      <button class='addCardButton' @click="changeAddCardForm(); $emit('addCard', newContent, listId)">Add card</button>
      <button class='cancelAddButton' @click="changeAddCardForm">X</button>
  </form>
  <IsCard class="Card" v-for="card in cardsFiltered" :card="card" :key="card.id"></IsCard>
</div>
</template>

<script>
import IsCard from './IsCard.vue'
export default {
  props: ['list', 'cards'],
  data () {
    return {
      addCardForm: false,
      cardsFiltered: [],
      newContent: '',
      listId: this.list.id
    }
  },
  watch: {
    cards: function (newVal, oldVal) {
      if (this.list !== null && newVal !== null) {
        // console.log('this cards is ', this.cards)
        // console.log('Prop changed: ', newVal, ' | was: ', oldVal)
        this.cardsFiltered = newVal.filter((card) => card.categories.includes(this.list.id))
      }
    },
    list: function (newVal, oldVal) {
      if (this.cards !== null && newVal !== null) {
        // console.log('Prop changed: ', newVal, ' | was: ', oldVal)
        this.cardsFiltered = this.cards.filter((card) => card.categories.includes(newVal.id))
      }
    }
  },
  components: {
    IsCard
  },
  methods: {
    changeAddCardForm () {
      this.addCardForm = !this.addCardForm
      console.log(this.addCardForm)
    }
  }

}
</script>

<style scoped>
#cardManager {
  background-color: rgb(247, 177, 169);
}
</style>
