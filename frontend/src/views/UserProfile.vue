<template>
  <div>
    <Navbar />
    <div
      id="nav"
      class="d-flex justify-content-start justify-content-sm-center mb-3 p-4"
    >
      <router-link to="/">
        <img
          id="logo"
          :src="require('../assets/logos/icon-left-font.png')"
          alt="logo Groupomania"
        />
      </router-link>
    </div>
    <b-row class="text-center justify-content-center">
      <div class="d-flex align-items-center flex-column">
        <ProfileImage
          :src="userProfile.imageUrl"
          customClass="profile-main-picture"
          divCustomClass="div-picture"
        />
        <p class="user-name">
          {{ userProfile.firstName }} {{ userProfile.lastName }}
        </p>
        
        <AdminDeleteAccount
          v-if="userData.admin && !userProfile.deleted"
          :userProfile="userProfile"
        />
      </div>
    </b-row>

    <PostsList :userId="this.$route.params.userId" />
  </div>
</template>

<script>
import { mapState, mapActions } from 'vuex'
import { apiClient } from '../services/ApiClient'
import ProfileImage from '../components/ProfileImage'
import PostsList from '../components/PostsList'
import AdminDeleteAccount from '../components/AdminDeleteAccount'
import Navbar from '../components/Navbar'

export default {
  name: 'UserProfile',
  components: {
    ProfileImage,
    PostsList,
    AdminDeleteAccount,
    Navbar
  },
  watch: {
    $route (to, from) {
      window.location.reload()
    }
  },
  data () {
    return {
      userProfile: {},
      userData: JSON.parse(localStorage.getItem('userData'))
    }
  },

  async mounted () {
    const res = await apiClient.get(`api/users/${this.$route.params.userId}/`)
    this.userProfile = res.user
  },
  methods: {}
}
</script>

<style lang="css">
.div-picture {
  width: 120px;
  height: 120px;
  overflow: hidden;
  border-radius: 100%;
  margin-bottom: 1rem;
  background: black;
  box-shadow: 0px 1px 2px 2px rgba(204, 204, 204, 0.5);
}

.profile-main-picture {
  height: 120px;
  transform: scale(1.35);
}

.user-name {
  font-size: 20px;
  font-weight: bold;
}

@media screen and (min-width: 280px) and (max-width: 767px) {
  .user-name {
    font-size: 16px;
  }
  
  .div-picture {
    width: 95px;
    height: 95px;
    overflow: hidden;
    border-radius: 100%;
    margin-bottom: 1rem;
    background: black;
    box-shadow: 0px 1px 2px 2px rgba(204, 204, 204, 0.5);
  }
  .profile-main-picture {
    height: 95px;
  }
}
</style>