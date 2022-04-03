<template>
<!-- <div='listManager'> -->
  <div class='addList block has-text-centered'>
    <h1 class="title my-5"> New Trello</h1>
  </div>
  <section class='hero'>
    <div id="listManager" class='columns'>
      <div
        class='column is-4'
        v-for='list in lists'
        :key='list.id'
      >
        <div class="has-background-warning box">
          <!-- <div class="columns">
            <p class='column is-four-fifths unselectable has-text-centered is-size-4 has-text-dark' @click="ShowEditListForm()" v-if="editListForm === false">{{ list.name }}</p>
            <form @submit.prevent='updateList(list)' v-else>
              <input class='input is-small is-warning has-background-warning has-text-centered is-size-4 has-text-dark' v-model='list.name' />
              <button class='button'>Confirm</button>
            </form>
            <div class="block mt-5">
              <button id='deleteButton' class='button has-background-warning is-small unselectable' @click='removeList(list.id)'>
              <img
                class="image" width="20" min-width="10"
                src="../assets/close.png"
                />
              </button>
            </div>
          </div> -->
          <CardManager id="cardManager" :list="list" :cards="cards"
          @ShowEditListForm="ShowEditListForm" v-model:listName="listName"
          @addCard="addCard" v-model="newContent" v-model:listId="listId"
          @delete-card="deleteCard" v-model:cardId="cardId"
          @edit-card="editCard" v-model:updateContent="updateContent"
          @updateList="updateList"
          @removeList="removeList"
          ></CardManager>
        </div>
      </div>
      <div class='column is-3 mx-2'>
        <button class='button' @click="ShowAddListForm()" v-if="addListForm === false">+ Add another list</button>
        <form class="addList" @submit.prevent='addList' v-else>
          <input class='input' v-model='newTitle' placeholder='Enter list title...' />
          <button class='button'>Add list</button>
          <button class='button' @click="CancelAddListForm()">
            <img
              class="image" width="12" min-width="10"
              src="../assets/close.png"
            />
          </button>
        </form>
      </div>
    </div>
  </section>
<!-- </div> -->
</template>

<script>
import CardManager from '../components/CardManager.vue'
export default {
  components: {
    CardManager
  },
  data () {
    return {
      addListForm: false,
      // editListForm: false,
      lists: [],
      CatId: 0,
      cards: [],
      value: '',
      newContent: '',
      newCards: [],
      amountComments: 0
    }
  },
  methods: {
    // =================== WORDPRESS CONNEXION ==================
    wpapiSetting () {
      const WPAPI = require('wpapi/superagent')
      const wp = new WPAPI({
        endpoint: 'http://localhost/wordpress/index.php/wp-json',
        username: 'LiChun',
        password: 'Qwer@1226'
      })
      return wp
    },
    // ================== LISTS METHODS ========================
    ShowAddListForm () {
      this.addListForm = true
    },
    CancelAddListForm () {
      this.addListForm = false
      this.newTitle = ''
    },
    // ShowEditListForm () {
    //   this.editListForm = true
    // },
    addList () {
      this.wpapiSetting().categories().create({
        name: this.newTitle,
        status: 'publish'
      })
        .then((response) => {
          console.log(response.id)
          this.lists.push(response)
        })
      this.newTitle = ''
      this.newList = ''
      this.addListForm = false
    },
    removeList (listId) {
      // console.log('what is this ', list)
      this.wpapiSetting().categories().id(listId).param('force', true).delete()
        .then((response) => {
          // console.log('in side response')
          // console.log(response)
          this.lists = [...this.lists.filter((element) => element.id !== listId)]
          this.wpapiSetting().posts().perPage(100).get()
            .then((cards) => {
              // console.log('my data here is ', cards)
              this.cards = cards
              // console.log('cards.get is ', this.cards)
            })
            .catch(function (err) {
            // handle error
              console.error('posts get ', err)
            })
          // console.log('cards is ', this.cards)
          // this.cards = [...this.cards]
        })
    },
    // onInput (e) {
    //   console.log(e.target.innerText)
    //   return e.target.innerText
    //   // loose focus
    // },
    updateList (listId, listName) {
      this.wpapiSetting().categories().id(listId).update({
        name: listName,
        status: 'publish'
      })
    },
    //     .then((response) => {
    //       console.log(response)
    //     })
    // },
    // ==================== CARDS METHODS =====================
    addCard (newContent, listId) {
      console.log('my newContent is ', newContent)
      console.log('my listId is ', listId)
      const WPAPI = require('wpapi/superagent')
      const wp = new WPAPI({
        endpoint: 'http://localhost/index.php/wp-json/',
        username: 'LiChun',
        password: 'Qwer@1226'
      })
      wp.posts().create({
        content: newContent,
        categories: listId,
        status: 'publish'
      }).then((response) => {
        console.log(response)
        this.cards.push(response)
        wp.posts().perPage(100).get()
          .then((cards) => {
            console.log('my data here 1 is ', cards)
            this.cards = cards
            // console.log('cards.get is ', this.cards)
          })
          .catch(function (err) {
            // handle error
            console.error('posts get ', err)
          })
        // this.cards = [...]
      })
    },
    deleteCard (cardId) {
      console.log('My cardId in listManager is ', cardId)
      this.wpapiSetting().posts().id(cardId).param('force', true).delete()
        .then((response) => {
          console.log('in side response')
          console.log('response is ', response)
          this.cards = [...this.cards.filter((element) => element.id !== cardId)]
          this.wpapiSetting().posts().perPage(100).get()
            .then((cards) => {
              this.cards = cards
            })
            .catch(function (err) {
              console.error('posts get ', err)
            })
          // console.log('cards is ', this.cards)
        })
    },
    editCard (updateContent, cardId) {
      console.log('updateContent is ', updateContent)
      console.log('cardId is ', cardId)
      this.wpapiSetting().posts().id(cardId).update({
        content: '<p>' + updateContent + '</p>',
        status: 'publish'
      })
        .then((response) => {
          console.log('in side response')
          console.log('response is ', response)
          this.cards = [...this.cards.filter((element) => element.id !== cardId)]
          this.wpapiSetting().posts().perPage(100).get()
            .then((cards) => {
              this.cards = cards
            })
            .catch(function (err) {
              console.error('posts get ', err)
            })
          // console.log('cards is ', this.cards)
        })
    },
    ShowAmountOfComments () {
      this.amountComments = this.commentsFiltered.length
    }
  },
  //  ================================================================
  mounted () {
    this.wpapiSetting().categories().perPage(100).get()
      .then((lists) => {
        this.lists = lists
        console.log('my list here 2 is ', this.lists)
        this.wpapiSetting().posts().perPage(100).get()
          .then((cards) => {
            console.log('my data here 2 is ', cards)
            this.cards = cards
            // console.log('cards.get is ', this.cards)
          })
          .catch(function (err) {
          // handle error
            console.error('posts get ', err)
          })
      })
      .catch(function (err) {
      // handle error
        console.error('categories get ', err)
      })
  }
}
</script>

<style >
/* .hero {
  display: grid;
  grid-template-rows: 1fr auto;
  height: 100vh;
} */
/* .addList {
  grid-row: 1 / 2;
} */
#listManager {
  /* grid-row: 2 / -1; */
  /* background-color: #99eeee; */
  overflow-x: scroll;
  /* width: 100%; */
  padding: 0 10px;
}
/*
.addListButton {
    width: 200px;
    font-size: 15px;
    background: transparent;
}
.addList {
  display: grid;
  grid-template-rows: 1fr 1fr;
  grid-template-columns: 1fr 1fr 1fr;
  width: 100%;
  height: 70px;
  padding: 2px;
  gap: 5px;
  background-color: antiquewhite;
}
.listTitle {
  grid-column: 1 / -1;
  width: 95%;
  font-size: 15px;
  margin-left: auto;
  margin-right: auto;
}
.addButton {
  grid-column: 1 / 2;
}
.cancelAddButton {
  grid-column: 2 / 3 ;
  font-size: 15px;
  background: transparent;
  border: none;
} */
/* textarea {
  width: 230px;
  background-color: transparent;
  border: none;
} */
</style>
