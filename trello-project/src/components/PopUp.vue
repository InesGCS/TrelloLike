<template>
<div class="popUp">
    <div class='popUpInner'>
        <slot />
        <!-- <p>({{this.commentsFiltered.length}})</p>
        {{this.$emit('name', this.commentsFiltered.length)}} -->
        <button class='addCommentButton' @click="ShowAddCommentForm" v-if="addCommentForm === false">+ Add another comment</button>
        <form class="addComment" @submit.prevent='addComment(card.id)' v-else>
          <input class='comment' v-model='newComment' placeholder='What to say...' />
          <button class='addButton'>Add comment</button>
          <button class='button' @click="CancelAddCommentForm()">
            <img
              class="image" width="12" min-width="10"
              src="../assets/close.png"
            />
          </button>
        </form>
        <IsComment class="comment" v-for="comment in commentsFiltered" :comment="comment"
        @removeComment="removeComment" v-model="commentId" @updateComment="updateComment"
        :newContent="newContent" :key="comment.id"></IsComment>
        <!-- <IsComment class="comment" v-for="comment in commentsFiltered" :comment="comment" @removeComment="removeComment" v-model="commentId" :key="comment.id"></IsComment> -->
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
      // number: this.commentsFiltered.length
    })
  },
  methods: {
    wpapiSetting () {
      const WPAPI = require('wpapi/superagent')
      const wp = new WPAPI({
        endpoint: 'http://localhost/index.php/wp-json/',
        username: 'Tanguy',
        password: 'ANP7qB$4CXx5SVWjo%'
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
      }).then((response) => {
        // console.log(response.id)
        this.commentsFiltered.push(response)
      })
      this.newComment = ''
      this.addCommentForm = false
    },
    removeComment (commentId) {
      console.log('comment ID is ', commentId)
      this.wpapiSetting().comments().id(commentId).param('force', true).delete()
        .then((response) => {
          this.wpapiSetting().comments().perPage(100).get()
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
      // const cardId = this.card.id
      // console.log('in the update comment function comment content is ', newContent)
      // console.log('in the update comment function comment id is ', commentId)
      this.wpapiSetting().comments().id(commentId).update({
        content: newContent,
        // post: cardId,
        status: 'publish'
      })
        .then((response) => {
          this.wpapiSetting().comments().perPage(100).get()
            .then((comments) => {
              this.comments = comments
              this.commentsFiltered = this.comments.filter((comment) => comment.post === this.card.id)
            })
        })
      // .then((response) => {
      //   console.log(response)
      // }).catch(error => {
      //   console.log('here is in the catch error')
      //   console.log(error)
      // })
    // },
    // CountComments () {
    //   const amountComment = this.commentsFiltered.length
    //   return amountComment
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
