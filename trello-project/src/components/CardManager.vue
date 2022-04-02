<template>
<div id="cardManager" class="has-text-centered">
  <IsCard class="box" v-for="card in cardsFiltered" :card="card" :key="card.id" />
  <button class='button is-warning mb-4' v-if="addCardForm === false" @click="changeAddCardForm" >+ Add another card</button>
  <form v-else @submit.prevent>
      <input class='input ' v-model="newContent" placeholder='Enter new content...' />
      <div class="is-flex is-flex-direction-row is-justify-content-space-between">
      <button class='button is-warning' @click="$emit('addCard', newContent, listId); changeAddCardForm()">Add card</button>
      <button class='button is-warning' @click="changeAddCardForm">X</button>
      </div>
  </form>
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
      this.newContent = ''
    }
  }

}
</script>

<style scoped>
/* #cardManager { */
  /* background-color: rgb(255, 255, 255); */
/* } */
.box {
    /* cursor: pointer; */
}
</style>
