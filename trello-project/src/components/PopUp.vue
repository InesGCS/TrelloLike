<template>
<div class="popUp">
    <div class='popUpInner'>
        <slot />
        <button class='addCommentButton' @click="ShowAddCommentForm" v-if="addCommentForm === false">+ Add another comment</button>
        <form class="addComment" @submit.prevent='addComment(card.id)' v-else>
          <input class='comment' v-model='newComment' placeholder='What to say...' />
          <button class='addButton'>Add comment</button>
          <button class='cancelAddButton' @click="CancelAddCommentForm()">X</button>
        </form>
        <IsComment class="comment" v-for="comment in commentsFiltered" :comment="comment" @removeComment="removeComment" v-model="commentId" :key="comment.id" @updateComment="updateComment" :newContent="newContent"></IsComment>
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
      commentsFiltered: [],
      addCommentForm: false
    })
  },
  methods: {
    wpapiSetting () {
      const WPAPI = require('wpapi/superagent')
      const wp = new WPAPI({
        endpoint: 'http://localhost/wordpress/index.php/wp-json/',
        username: 'hyris',
        password: 'hyris2022'
      })
      return wp
    },
    ShowAddCommentForm () {
      this.addCommentForm = true
    },
    CancelAddCommentForm () {
      this.addCommentForm = false
      this.newComment = ''
    },
    addComment (cardId) {
      console.log('card id is ', cardId)
      this.wpapiSetting().comments().create({
        content: this.newComment,
        post: cardId,
        status: 'publish'
      })
        .then((response) => {
          // console.log(response.id)
          this.commentsFiltered.push(response)
        })
      this.newComment = ''
      this.addCommentForm = false
    },
    removeComment (commentId) {
      this.wpapiSetting().comments().id(commentId).param('force', true).delete()
        .then((response) => {
          this.wpapiSetting().comments().get()
            .then((comments) => {
              // console.log('comments are ', comments)
              this.comments = comments
              // console.log('this comment is changed to ', this.comments)
              // console.log('this card id is ', this.card.id)
              this.commentsFiltered = this.comments.filter((comment) => comment.post === this.card.id)
            })
        })
    },
    updateComment (newContent, commentId) {
      this.wpapiSetting().comments().id(commentId).update({
        content: newContent,
        status: 'publish'
      })
    }
  },
  mounted () {
    this.wpapiSetting().comments().get()
      .then((comments) => {
        console.log('comments are ', comments)
        this.comments = comments
        console.log('this comment is changed to ', this.comments)
        console.log('this card id is ', this.card.id)
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
