<template>
  <div>
    <b-button
      v-if="count > 1 && !allCommentsDisplayed"
      @click="fetchAllComments"
      class="display-comments mb-2 pt-0 d-flex text-left"
      ><span v-if="count > 2"
        >Afficher {{ count - 1 }} autres commentaires</span
      >
      <span v-else>Afficher {{ count - 1 }} autre commentaire</span></b-button
    >
    <div class="comment mb-2 text-left" v-for="comment in list" :key="comment.message">
      <Comment
        @commentDeleted="removeComment"
        @displayNotification="displayNotification"
        :comment="comment"
        :post="post"
      />
    </div>

    <CreateComment @commentCreated="appendComment" :post="post" />
  </div>
</template>

<script>
import { apiClient } from '../services/ApiClient'
import router from '../router/index'
import { mapState, mapActions } from 'vuex'
import PostsList from '../components/PostsList'
import CreateComment from './CreateComment'
import Comment from './Comment'

export default {
  name: 'CommentsList',
  components: {
    CreateComment,
    Comment
  },
  props: ['post'],
  data () {
    return {
      list: [],
      count: null,
      allCommentsDisplayed: false
    }
  },
  async mounted () {
    const res = await apiClient.get(
      `api/posts/${this.post.id}/comments/?limit=1`
    )
    this.list = res.comments.rows
    this.count = res.comments.count
  },
  methods: {
    async fetchAllComments () {
      const res = await apiClient.get(`api/posts/${this.post.id}/comments/`)
      this.list = res.comments.rows
      this.allCommentsDisplayed = true
    },
    appendComment (comment) {
      this.list.push(comment)
    },
    removeComment (commentToDelete) {
      this.list = this.list.filter(comment => comment.id !== commentToDelete.id)
    },
    displayNotification (text) {
      this.$bvToast.toast(text, {
        title: 'Notification',
        autoHideDelay: 4000
      })
    }
  },
  computed: {}
}
</script>

<style lang="css">

.comment-box {
  background-color: rgba(108, 117, 125, 0.1);
  padding: 0.375rem 0.75rem;
  border-radius: 0.25rem;
  margin-bottom: 0;
}

.display-comments {
  color: #747474;
}

.display-comments:hover {
  text-decoration: underline;
}

.display-comments::hover{
  color: #747474 !important;
  background: none !important;
}

.display-comments::focus{
  color: #747474 !important;
  background: none !important;
}

.display-comments::active {
  color: #747474 !important;
  background: none !important;
}
</style>