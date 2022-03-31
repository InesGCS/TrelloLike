<template>
<div id="listManager">
  <button class='addListButton' @click="ShowAddListForm()" v-if="addListForm === false">+ Add another list</button>
  <form @submit.prevent='addList' v-else>
      <input class='listTitle' v-model='newTitle' placeholder='Enter list title...' />
      <button class='addButton'>Add list</button>
      <button class='cancelAddButton' @click="CancelAddListForm()">X</button>
  </form>
  <div
    class='lists'
    v-for='list in lists'
    :key='list.id'
  >

    <p class='unselectable'>{{ list.name }}</p>
    <button id='deleteButton' class='unselectable' @click='removePost(list.id)'>X</button>
  <CardManager :list="list" :cards="cards"></CardManager>
  <br>
  ======================================================
  <br>

  </div>
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
      lists: [],
      CatId: 0,
      cards: []

    }
  },
  methods: {
    wpapiSetting () {
      const WPAPI = require('wpapi/superagent')
      const wp = new WPAPI({
        endpoint: 'http://localhost/wordpress/index.php/wp-json/',
        username: 'LiChun',
        password: 'Qwer@1226'
      })
      return wp
    },
    ShowAddListForm () {
      this.addListForm = true
    },
    CancelAddListForm () {
      this.addListForm = false
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
      console.log('what is this ', list)
      this.wpapiSetting().categories().id(list).param('force', true).delete()
        .then((response) => {
          console.log('in side response')
          console.log(response)
          this.lists = [...this.lists.filter((element) => element.id !== list)]
          this.wpapiSetting().posts().get()
            .then((cards) => {
              console.log('my data here is ', cards)
              this.cards = cards
              console.log('cards.get is ', this.cards)
            })
            .catch(function (err) {
            // handle error
              console.error('posts get ', err)
            })
          console.log('cards is ', this.cards)
          // this.cards = [...this.cards]
        })
    },
    onInput (e) {
      console.log(e.target.innerText)
    },
    updateList (list) {
      console.log('inside updateList function')
      this.wpapiSetting().categories().id(list).update({
        name: list.name,
        status: 'publish'
      })
        .then((response) => {
          console.log(response)
        })
    }
  },
  // async mounted () {
  //   const categories = await this.wpapiSetting().categories().get()
  //   const posts = await this.wpapiSetting().posts().get()
  //   console.log('categories is : ', categories)
  //   console.log('post is : ', posts)
  //   // console.log(categories[0].id)
  //   this.lists = categories
  //   this.cards = posts
  //   console.log('lists is : ', this.lists)
  //   console.log('cards is : ', this.cards)
  //   console.log('card is : ', this.card)
  //   // this.cards = posts.filter((card) => card.categories.includes(this.list.id))
  // },

  mounted () {
    const WPAPI = require('wpapi/superagent')
    const wp = new WPAPI({
      endpoint: 'http://localhost/wordpress/index.php/wp-json',
      username: 'hyris',
      password: 'hyris2022'
    })
    wp.categories().get()
      .then((lists) => {
        this.lists = lists
        // for (let i = 0; i < data.length; i++) {
        //   this.list.id = data[i].id
        // }
        wp.posts().get()
          .then((cards) => {
            console.log('my data here is ', cards)
            this.cards = cards
            console.log('cards.get is ', this.cards)
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
#listManager {
  background-color: #99eeee;
}
.addListButton {
    width: 200px;
    font-size: 15px;
    background: transparent;
}

form {
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
