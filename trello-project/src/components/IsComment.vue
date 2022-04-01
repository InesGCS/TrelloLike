<template>
<div id="isComment">
    <p v-html="comment.content.rendered"></p>
    <button id='deleteCommentButton' @click="$emit('removeComment', commentId)">X</button>
</div>
</template>

<script>
export default {
  props: ['comment'],

  data () {
    return {
      commentId: this.comment.id
    }
  }
}

</script>
