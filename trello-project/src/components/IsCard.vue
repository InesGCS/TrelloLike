<template>
<div id="isCard">
    <p @click="() => TogglePopUp('buttonTrigger')" v-if="changeContent === false" v-html="card.content.rendered"></p>
    <input v-if="changeContent === true" v-model="updateContent">
    <button class='button has-background-warning mx-2' v-if="changeContent === false" @click="changeContentFunction">Edit</button>
    <button class='button has-background-warning mx-2' v-if="changeContent === true" @click="this.$parent.$emit('editCard', updateContent, cardId); changeContentFunction()">Edit</button>
    <button class='button has-background-warning mx-2
    ' v-if="changeContent === false" @click="this.$parent.$emit('deleteCard', cardId)">X</button>
    <PopUp
    v-if="popupTrigger.buttonTrigger"
    :TogglePopUp="() => TogglePopUp('buttonTrigger')"
    :card="card">
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
      updateContent: this.card.content.rendered.replace(/<\/?[^>]+(>|$)/g, '')
    }
  },
  methods: {
    changeContentFunction () {
      this.changeContent = !this.changeContent
      if (this.changeContent === false) {
        this.updateContent = this.card.content.rendered.replace(/<\/?[^>]+(>|$)/g, '')
      }
    }
  }
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
