<template>
<div id="isCard">
    <p @click="() => TogglePopUp('buttonTrigger')" v-if="changeContent === false" v-html="card.content.rendered"></p>
    <p>({{this.commentsFiltered.length}})</p>
    <input v-if="changeContent === true" v-model="updateContent">
    <button class='button has-background-warning mx-2' v-if="changeContent === false" @click="changeContentFunction">Edit</button>
    <button class='button has-background-warning mx-2' v-if="changeContent === true" @click="this.$parent.$emit('editCard', updateContent, cardId); changeContentFunction()">Edit</button>
    <button class='button has-background-warning mx-2
    ' v-if="changeContent === false" @click="this.$parent.$emit('deleteCard', cardId)">
      <img
        class="image" width="12" min-width="10"
        src="../assets/close.png"
      />
    </button>
    <PopUp
    v-if="popupTrigger.buttonTrigger"
    :TogglePopUp="() => TogglePopUp('buttonTrigger')"
    :card="card"
    @name="GetAmountofComments">
      <h1 v-html="card.content.rendered"></h1>
    </PopUp>

</div>
</template>

<script>
import { ref } from 'vue'
import PopUp from '../components/PopUp.vue'
export default {
  props: ['card'],
  components: {
    PopUp
  },
  setup () {
    const popupTrigger = ref({
      buttonTrigger: false
    })
    const TogglePopUp = (trigger) => {
      popupTrigger.value[trigger] = !popupTrigger.value[trigger]
    }
    return {
      popupTrigger,
      TogglePopUp
    }
  },
  data () {
    return {
      cardId: this.card.id,
      changeContent: false,
      commentsFiltered: [],
      comments: [],
      updateContent: this.card.content.rendered.replace(/<\/?[^>]+(>|$)/g, '')
    }
  },
  methods: {
    wpapiSetting () {
      const WPAPI = require('wpapi/superagent')
      const wp = new WPAPI({
        endpoint: 'http://localhost/wordpress/index.php/wp-json',
        username: 'hyris',
        password: 'hyris2022'
      })
      return wp
    },
    changeContentFunction () {
      this.changeContent = !this.changeContent
      if (this.changeContent === false) {
        this.updateContent = this.card.content.rendered.replace(/<\/?[^>]+(>|$)/g, '')
      }
    }
  },
  mounted () {
    this.wpapiSetting().comments().perPage(100).get()
      .then((comments) => {
        console.log('comments are ', comments)
        this.comments = comments
        console.log('this comment is changed to ', this.comments)
        console.log('this card id is ', this.card.id)
        this.commentsFiltered = this.comments.filter((comment) => comment.post === this.card.id)
      })
  }
  // GetAmountofComments (value) {
  //   console.log('test', value)
}
</script>

<style scoped>
#isCard {
  /* background-color: lemonchiffon; */
  /* cursor: pointer; */
}
p:hover {
  background-color: #e1e1ea;
}
</style>
