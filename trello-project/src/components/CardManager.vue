<template>
<<<<<<< HEAD
<div id="cardManager">
<h2>I'm the card Manager !</h2>
{{ cards }}
<IsCard class="Card" v-for="card in cardManager" :card="card" :key="card.id"> My cards </IsCard>
</div>
</template>

<script>
import IsCard from './IsCard.vue'
export default {
  components: {
    IsCard
  },
  data: () => {
    return ({
      array: wp,
      cards: ['']
    })
  }
}
const WPAPI = require('wpapi/superagent')
const wp = new WPAPI({
  endpoint: 'http://localhost/wordpress/index.php/wp-json',
  username: 'hyris',
  password: 'hyris2022'
})

wp.posts().get()
  .then(function (data) {
    // do something with the returned cards
    console.log(data)
    this.cards = data
    console.log(this.cards)
    return this.cards
  })
  .catch(function (err) {
    // handle error
    console.log(err)
  })

wp.posts().create({
  title: 'mon titre blablabla',
  content: 'BLABLABLA'
})
</script>
=======
    <h1>I am from CardManager</h1>
</template>
>>>>>>> 517c38677f3818d99e8362dccf100fc66b396e4b
