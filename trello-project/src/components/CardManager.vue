<template>
<div id="cardManager" class="has-text-centered">
  <!-- ============================================================================== -->
      <div class="is-flex is-flex-direction-row is-justify-content-space-between">
            <p class='column is-four-fifths unselectable has-text-centered is-size-4 has-text-dark' @click="ChangeEditListForm" v-if="editListForm === false">{{ listName }}</p>
            <form @submit.prevent="$emit('updateList', listId, listName); ChangeEditListForm()" v-else>
              <input class='input is-small is-warning has-background-warning has-text-centered is-size-4 has-text-dark' v-model='listName' />
              <button class='button'>Confirm</button>
            </form>
              <button id='deleteButton' class='button has-background-warning is-small unselectable' @click="$emit('removeList', listId)">
              <img
                class="image" width="20" min-width="10"
                src="../assets/close.png"
                />
              </button>
          </div>
   <!-- ============================================================================== -->
  <IsCard class="box" v-for="card in cardsFiltered" :card="card" :key="card.id" />
  <button class='button is-warning mb-4' v-if="addCardForm === false" @click="changeAddCardForm" >+ Add another card</button>
  <form v-else @submit.prevent>
      <input class='input ' v-model="newContent" placeholder='Enter new content...' />
      <div class="is-flex is-flex-direction-row is-justify-content-space-between">
      <button class='button is-warning' @click="$emit('addCard', newContent, listId); changeAddCardForm()">Add card</button>
      <button class='button is-warning' @click="changeAddCardForm">
        <img
          class="image" width="12" min-width="10"
          src="../assets/close.png"
        />
      </button>
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
      addListForm: false,
      editListForm: false,
      addCardForm: false,
      cardsFiltered: [],
      newContent: '',
      listId: this.list.id,
      listName: this.list.name
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
    ShowAddListForm () {
      this.addListForm = true
    },
    CancelAddListForm () {
      this.addListForm = false
      this.newTitle = ''
    },
    ChangeEditListForm () {
      this.editListForm = !this.editListForm
    },
    changeAddCardForm () {
      this.addCardForm = !this.addCardForm
      this.newContent = ''
    },
    CountComments () {
      const amountComments = this.comments.lengh
      console.log(amountComments)
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
