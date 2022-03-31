<template>
<div id="cardManager">
<h2>I'm the card Manager !</h2>
<button class='addCardButton' v-if="addCardForm === false" @click="changeAddCardForm" >+ Add another card</button>
  <form v-else>
      <input class='cardContent' v-model="newContent" placeholder='Enter new content...' />
      <button class='addCardButton' @click="addCard">Add card</button>
      <button class='cancelAddButton'>X</button>
  </form>
  <IsCard class="Card" v-for="card in cardsFiltered" :card="card" :key="card.id"></IsCard>
</div>
</template>

<script>
import IsCard from './IsCard.vue'
export default {
  props: ['list', 'cards'],
  data: () => {
    return ({
      // cards: [],
      addCardForm: false,
      cardsFiltered: []
    })
  },
  watch: {
    cards: function (newVal, oldVal) {
      if (this.list !== null && newVal !== null) {
        console.log('Prop changed: ', newVal, ' | was: ', oldVal)
        this.cardsFiltered = newVal.filter((card) => card.categories.includes(this.list.id))
      }
    },
    list: function (newVal, oldVal) {
      if (this.cards !== null && newVal !== null) {
        console.log('Prop changed: ', newVal, ' | was: ', oldVal)
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
    },
    addCard: () => {
      const WPAPI = require('wpapi/superagent')
      const wp = new WPAPI({
        endpoint: 'http://localhost/wordpress/index.php/wp-json',
        username: 'hyris',
        password: 'hyris2022'
      })
      wp.posts().create({
        content: this.newContent
      }).then(function (response) {
        console.log(response.id)
      })
    }
  }

}
</script>

<style scoped>
#cardManager {
  background-color: rgb(247, 177, 169);
}
</style>
