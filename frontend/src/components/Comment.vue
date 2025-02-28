<template>
  <div>
    <div class="d-flex align-items-center position-relative">
      <router-link
        :to="{ name: 'UserProfile', params: { userId: comment.User.id } }"
        ><div class="d-flex text-center mr-2 mt-2">
          <ProfileImage
            :src="comment.User.imageUrl"
            customClass="comment-profile-picture"
            divCustomClass="div-comment-picture"
          /></div
      ></router-link>
      <div class="comment-box">
        <router-link
          :to="{ name: 'UserProfile', params: { userId: comment.User.id } }"
          ><p class="mb-0 font-weight-bold">
            {{ comment.User.firstName }} {{ comment.User.lastName }}
          </p></router-link
        >
        <input
          v-if="isEditing"
          ref="inputContent"
          v-model="comment.content"
          @keydown.enter.exact.prevent
          @keyup.enter.exact="modifyComment"
          @keydown.enter.shift.exact="newline"
          type="text"
          class="input-content border-0 my-2"
        />
        <p v-else class="mb-0">{{ comment.content }}</p>
      </div>

      <EditButton
        customClass="comment-button"
        classCollapse="collapse-button"
        :isCreator="comment.User.id == userData.id"
        :isAdmin="userData.admin"
        @clickedEditButton="startEditing"
        @onDelete="onDelete"
        modifyText=" Modifier"
        deleteText=" Supprimer"
      >
      </EditButton>
    </div>
    <p class="text-secondary comment-date">
      {{
        moment(comment.updatedAt)
          .locale('fr')
          .fromNow()
      }}
    </p>
  </div>
</template>

<script>
import { apiClient } from '../services/ApiClient'
import EditButton from './EditButton'
import ProfileImage from './ProfileImage'

export default {
  name: 'Comment',
  components: {
    EditButton,
    ProfileImage
  },
  props: ['comment', 'post'],
  data () {
    return {
      userData: JSON.parse(localStorage.getItem('userData')),
      isEditing: false
    }
  },
  methods: {
    toggleActions () {
      this.areActionsVisible = !this.areActionsVisible
    },

    async onDelete () {
      await apiClient.delete(
        `api/posts/${this.post.id}/comments/${this.comment.id}`
      )
      this.$emit('commentDeleted', this.comment)
      this.$emit('displayNotification', 'Commentaire supprimé !')
    },

    startEditing () {
      this.isEditing = true
      setTimeout(() => {
        this.$refs.inputContent.focus()
      }, 30)
    },
    newline () {
      this.comment.content = `${this.comment.content}\n`
    },

    async modifyComment () {
      const res = await apiClient.put(
        `api/posts/${this.post.id}/comments/${this.comment.id}`,
        { content: this.comment.content }
      )
      this.comment.updatedAt = res.comment.updatedAt
      this.isEditing = false
      this.$emit('displayNotification', 'Commentaire modifié !')
    }
  }
}
</script>

<style lang="css">
.div-comment-picture {
  margin-right: 0.5rem;
}

.comment-button {
  position: static !important;
  margin-left: 10px;
}

.collapse-button {
  right: 410px;
  top: 0px;
  background-color: #fff;
  box-shadow: 0rem 0.2rem 0.5rem rgba(0, 0, 0, 0.08);
  padding: 5px;
}

.input-content:focus {
  border-radius: 0.25rem;
  outline: none;
  box-shadow: 0 0 0 0.2rem rgba(0, 123, 255, 0.25);
}

.comment-date {
  margin-left: 58px;
  font-size: 0.8rem;
}

@media screen and (max-width: 1440px){
  .collapse-button {
    right: 180px;
  }
}

@media screen and (max-width: 1439px){
  .collapse-button {
    right: 100px;
  }
}

@media screen and (max-width: 1245px){
  .collapse-button {
    right: 50px;
  }
}

@media screen and (max-width: 1163px){
  .collapse-button {
    right: 0px;
  }
}

@media screen and (max-width: 1041px){
  .collapse-button {
    right: -30px;
  }
}

@media screen and (max-width: 991px){
  .collapse-button {
    right: 450px;
  }
}

@media screen and (max-width: 947px){
  .collapse-button {
    right: 350px;
  }
}

@media screen and (max-width: 841px){
  .collapse-button {
    right: 250px;
  }
}


@media screen and (max-width: 681px){
  .collapse-button {
    right: 150px;
  }
}


@media screen and (max-width: 585px){
  .collapse-button {
    right: 0px;
  }
}

@media screen and (min-width: 280px) and (max-width: 767px) {
  .comment-date {
    font-size: 0.6rem;
  }

  .comment-button {
    margin-bottom: 0;
    margin-left: 3px;
  }
}
</style>