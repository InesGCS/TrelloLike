<template>
<div id="cardManager">
<h2>I'm the card Manager !</h2>
<button class='addCardButton' v-if="addCardForm === false" @click="changeAddCardForm" >+ Add another card</button>
  <form v-else>
      <input class='cardContent' v-model="newContent" placeholder='Enter new content...' />
      <button class='addCardButton' @click="addCard">Add card</button>
      <button class='cancelAddButton'>X</button>
  </form>
  <!-- <div v-if="list.id == cards.categories[0]"> -->
    <!-- {{list[1].id}} -->
<IsCard class="Card" v-for="card in cards" card="card" :key="card.id"></IsCard>
<!-- </div> -->
</div>
</template>

<script>
import IsCard from './IsCard.vue'
export default {
  props: ['list'],
  components: {
    IsCard
  },
  data: () => {
    return ({
      cards: [],
      addCardForm: false
    })
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
  },
  mounted () {
    const WPAPI = require('wpapi/superagent')
    const wp = new WPAPI({
      endpoint: 'http://localhost/wordpress/index.php/wp-json',
      username: 'hyris',
      password: 'hyris2022'
    })

    wp.posts().get()
      .then((data) => {
        console.log('cards is ', data)
        this.cards = data
        this.cards = data
        console.log('this cards is : ', data)
      })
      .catch(function (err) {
      // handle error
        console.log(err)
      })

    wp.posts().create({
      title: 'mon titre blablabla',
      content: 'BLABLABLA'
    })
  }
}
</script>

<style scoped>
#cardManager {
  background-color: rgb(247, 177, 169);
}
</style>
