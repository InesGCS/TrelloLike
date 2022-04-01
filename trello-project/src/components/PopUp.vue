<template>
<div class="popUp">
    <div class='popUpInner'>
        <slot />
        <!-- <p>Lorem ipsum dolor sit amet, consectetur adipiscing elit. Morbi feugiat justo justo, ut convallis urna volutpat vel. Maecenas dapibus nunc in elit facilisis convallis. Etiam sed erat facilisis, efficitur nisl. </p> -->
        <!-- <form class="addComment" @submit.prevent='addComment'>
        <input class='listTitle' v-model='newTitle' placeholder='Enter list title...' />
        <button class='addButton'>Add list</button>
        <button class='cancelAddButton' @click="CancelAddListForm()">X</button>
        </form> -->
        {{card.id}}
        <IsComment class="comment" v-for="comment in commentsFiltered" :comment="comment" :key="comment.id"></IsComment>
        <button class="popUp-close" @click="TogglePopUp('buttonTrigger')">
            close
        </button>
    </div>
</div>
</template>

<script>
import IsComment from '../components/IsComment.vue'
export default {
  props: ['card', 'TogglePopUp'],
  components: {
    IsComment
  },
  data: () => {
    return ({
      comments: [],
      commentsFiltered: []
    })
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
    }
  },
  removeComment (commentId) {
    console.log('what is this ', commentId)
    this.wpapiSetting().categories().id(commentId).param('force', true).delete()
      .then((response) => {
        console.log('in side response')
        console.log(response)
        // this.lists = [...this.lists.filter((element) => element.id !== commentId)]
        // this.wpapiSetting().posts().get()
        //   .then((cards) => {
        //     console.log('my data here is ', cards)
        //     this.cards = cards
        //     console.log('cards.get is ', this.cards)
        //   })
        //   .catch(function (err) {
        //   // handle error
        //     console.error('posts get ', err)
        //   })
        // console.log('cards is ', this.cards)
        // // this.cards = [...this.cards]
      })
  },
  mounted () {
    this.wpapiSetting().comments().get()
      .then((comments) => {
        // console.log('comments are ', comments)
        this.comments = comments
        // console.log('this comment is changed to ', this.comments)
        // console.log('this card id is ', this.card.id)
        this.commentsFiltered = this.comments.filter((comment) => comment.post === this.card.id)
      })
  }
}

</script>

<style>
.popUp {
    position:fixed;
    top: 0;
    left: 0;
    right: 0;
    bottom: 0;
    z-index: 99;
    background-color: rgba(0, 0, 0, 0.2);

    display: flex;
    align-items: center;
    justify-content: center;
}

.popUpInner {
    background: #fff;
    padding: 32px;
}
</style>
