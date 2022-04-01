<template>
<div id="isComment">
    <p @click="ShowEditCommentForm()" v-if="editCommentForm === false" v-html="comment.content.rendered"></p>
    <form v-else>
      <input class='comment' v-model="newContent"/>
      <button class='editConfirmButton' @click="$emit('updateComment', newContent, comment.id)">Confirm</button>
      <button class='cancelEditButton' @click="CancelEditCommentForm()">X</button>
    </form>
    <button id='deleteCommentButton' @click="$emit('removeComment', comment.id)">X</button>
</div>
</template>

<script>
export default {
  props: ['comment'],

  data () {
    return {
      newContent: this.comment.content.rendered.replace(/<\/?[^>]+(>|$)/g, ''),
      commnetId: this.comment.id,
      editCommentForm: false

    }
  },
  methods: {
    ShowEditCommentForm () {
      this.editCommentForm = true
    },
    CancelEditCommentForm () {
      this.editCommentForm = false
      this.newContent = ''
    }
  }
}

</script>
