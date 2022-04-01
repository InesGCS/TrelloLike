<template>
<div>
  <div class='addList'>
  <button class='addListButton' @click="ShowAddListForm()" v-if="addListForm === false">+ Add another list</button>
  <form class="addList" @submit.prevent='addList' v-else>
      <input class='listTitle' v-model='newTitle' placeholder='Enter list title...' />
      <button class='addButton'>Add list</button>
      <button class='cancelAddButton' @click="CancelAddListForm()">X</button>
  </form>
  </div>
<section class='hero'>
  <div id="listManager" class='columns'>
  <div
    class='column'
    v-for='list in lists'
    :key='list.id'
  >
    <!-- <div v-click-outside="updateList(list)"> -->
    <!-- <p class='unselectable' contenteditable
    @input="onInput">{{ list.name }}</p> -->
    <!-- <textarea class='unselectable' v-model="list.name"></textarea> -->
    <p class='unselectable' @click="ShowEditListForm()" v-if="editListForm === false">{{ list.name }}</p>
    <form @submit.prevent='updateList(list)' v-else>
      <input class='listTitle' v-model='list.name' />
      <button class='editConfirmButton'>Confirm</button>
      <button class='cancelEditButton' @click="CancelEditListForm()">X</button>
    </form>
    <button id='deleteButton' class='unselectable' @click='removeList(list.id)'>X</button>
  <CardManager :list="list" :cards="cards"
  @addCard="addCard" v-model="newContent" v-model:listId="listId"
  @delete-card="deleteCard" v-model:cardId="cardId"
  ></CardManager>
  <br>
  ======================================================
  <br>

  </div>
</div>
</section>
</div>
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
      editListForm: false,
      lists: [],
      CatId: 0,
      cards: [],
      value: '',
      newContent: '',
      newCards: []
    }
  },
  methods: {
    // =================== WORDPRESS CONNEXION ==================
    wpapiSetting () {
      const WPAPI = require('wpapi/superagent')
      const wp = new WPAPI({
        endpoint: 'http://localhost/wordpress/index.php/wp-json/',
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
    ShowEditListForm () {
      this.editListForm = true
    },
    CancelEditListForm () {
      this.editListForm = false
    },
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
    removeList (list) {
      // console.log('what is this ', list)
      this.wpapiSetting().categories().id(list).param('force', true).delete()
        .then((response) => {
          // console.log('in side response')
          // console.log(response)
          this.lists = [...this.lists.filter((element) => element.id !== list)]
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
    updateList (list) {
      this.wpapiSetting().categories().id(list.id).update({
        name: list.name,
        status: 'publish'
      })
      this.editListForm = false
    },
    //     .then((response) => {
    //       console.log(response)
    //     })
    // },
    // ==================== CARDS METHODS =====================
    addCard (newContent, listId) {
      console.log('my newContent is ', newContent)
      const WPAPI = require('wpapi/superagent')
      const wp = new WPAPI({
        endpoint: 'http://localhost/wordpress/index.php/wp-json',
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
.addList {
  grid-row: 1 / 2;
}
#listManager {
  grid-row: 2 / -1;
  background-color: #99eeee;
  overflow-x: scroll;
}
.addListButton {
    width: 200px;
    font-size: 15px;
    background: transparent;
}

.addList {
  display: grid;
  grid-template-rows: 1fr 1fr;
  grid-template-columns: 1fr 1fr 1fr;
  width: 200px;
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
}

/* textarea {
  width: 230px;
  background-color: transparent;
  border: none;
} */

</style>
