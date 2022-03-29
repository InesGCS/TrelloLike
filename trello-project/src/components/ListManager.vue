<template>
<div>
  <CardManager></CardManager>
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
      CatId: 0
    }
  },
  methods: {
    ShowAddListForm () {
      this.addListForm = true
    },
    CancelAddListForm () {
      this.addListForm = false
    },
    // getMaxListId () {
    //   const keys = Object.keys(localStorage)
    //   let i = keys.length
    //   let max = 0
    //   while (i--) {
    //     const listId = parseInt(keys[i])
    //     if (Number.isInteger(listId) && listId > max) {
    //       max = listId
    //     }
    //   }
    //   return (max)
    // },
    addList () {
      const newId = this.getMaxListId() + 1
      const newList = {
        id: newId,
        listTitle: this.newTitle
      }
      // localStorage.setItem(newId, JSON.stringify(newList))
      this.lists.push(newList)
      this.newTitle = ''
      this.newList = ''
      this.addListForm = false
    }
    // storeList () {
    //   const keys = Object.keys(localStorage)
    //   let i = keys.length
    //   while (i--) {
    //     this.lists.push(JSON.parse(localStorage.getItem(keys[i])))
    //   }
    // }
  },
  async mounted () {
    // this.storeList()
    const WPAPI = require('wpapi')
    const wp = new WPAPI({
      endpoint: 'http://localhost/wordpress/index.php/wp-json/',
      username: 'LiChun',
      password: 'Qwer@1226'
    })
    const categories = await wp.categories().get()
    console.log(categories)
    console.log(categories[0].id)
    this.lists = categories
    // for (let i = 0; i < categories.length; i++) {
    //   this.lists.push(categories[i].name)
    // }
    // this.CatId = data[0].id
    // console.log('in wp categories ' + this.CatId)
    // return this.CatId
    // wp.categories().create({
    //   name: this.newTitle,
    //   status: 'publish'
    // })
    //   .then(function (response) {
    //     console.log(response.id)
    //   })
  }
}
</script>

<style >

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

</style>
