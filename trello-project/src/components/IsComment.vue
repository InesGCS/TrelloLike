<template>
<div id="isComment">
    <p @click="ShowEditCommentForm()" v-if="editCommentForm === false" v-html="comment.content.rendered"></p>
    <form v-else @submit.prevent>
      <input class='comment' v-model="newContent"/>
      <button class='button' @click="$emit('updateComment', newContent, comment.id); ConfirmEditCommentForm()">Confirm</button>
      <button class='button' @click="CancelEditCommentForm()">
        <img
          class="image" width="12" min-width="10"
          src="../assets/close.png"
        />
      </button>
    </form>
    <button class='button' @click="$emit('removeComment', comment.id)">
      <!-- <img
        class="image" width="12" min-width="10"
        src="../assets/close.png"
     /> -->
     Delete comment
    </button>
    <br><br>
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
      this.newContent = this.comment.content.rendered.replace(/<\/?[^>]+(>|$)/g, '')
    },
    ConfirmEditCommentForm () {
      this.editCommentForm = false
    }
  }
}

</script>
