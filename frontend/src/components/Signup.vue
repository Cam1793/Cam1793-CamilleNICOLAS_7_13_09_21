<template>
  <div class="page-container-signup">
    <b-container fluid>
      <b-row class="text-center justify-content-center">
        <b-col cols="12" lg="4">
          <b-card id="card-signup" class="border-0 shadow p-3 mb-5 mt-3 bg-white">
            <div class="pt-sm-3 pt-lg-0">
              <b-card-text class="login-text h4"><p id="login-title">S'INSCRIRE</p></b-card-text>
              <b-form>
                <b-form-group>
                  <b-form-input
                    id="firstName"
                    type="text"
                    placeholder="Prénom"
                    v-model="input.firstName"
                    class="account-input text-dark mb-2 mt-4 pl-3 w-100"
                  ></b-form-input>
                  <b-form-input
                    id="lastName"
                    type="text"
                    placeholder="Nom"
                    v-model="input.lastName"
                    class="account-input text-dark mb-2 pl-3 w-100"
                  ></b-form-input>
                  <b-form-input
                    id="email"
                    type="email"
                    placeholder="Email"
                    v-model="input.email"
                    class="account-input text-dark mb-2 pl-3 w-100"
                  ></b-form-input>
                  <b-form-input
                    id="password"
                    type="password"
                    placeholder="Mot de passe"
                    v-model="input.password"
                    class="account-input text-dark mb-2 pl-3 w-100"
                  ></b-form-input>
                </b-form-group>

                <b-button
                  v-on:click.stop="signup()"
                  type="submit"
                  id="signup-button"
                  class="account-btn font-weight-bold"
                >
                  Inscription
                </b-button>
                <p class="my-3 text-danger">{{ errorMessage }}</p>
              </b-form>
            </div>
            <div class="line my-3"></div>

            <p class="font-small grey-text d-flex justify-content-center mb-1">
              Vous avez déjà un compte ?
              <router-link to="/login" class="font-weight-bold ml-1">
                <p id="create-account">Se connecter</p></router-link
              >
            </p>
          </b-card>
        </b-col>
      </b-row>
    </b-container>
  </div>
</template>

<script>
import { apiClient } from '../services/ApiClient'
import router from '../router/index'

export default {
  name: 'Signup',
  data () {
    return {
      errorMessage: '',
      input: {
        firstName: '',
        lastName: '',
        email: '',
        password: ''
      }
    }
  },
  methods: {
    signup () {
      if (
        this.input.firstName != '' &&
        this.input.lastName != '' &&
        this.input.email != '' &&
        this.input.password != ''
      ) {
        apiClient
          .post('api/auth/signup', this.input)
          .then(data => {
            if (!data.token) {
              this.errorMessage = data.error.errors[0].message
            } else {
              localStorage.setItem('userToken', data.token)
              localStorage.setItem('userData', JSON.stringify(data.user))
              router.push({ name: 'Posts' })
            }
          })
          .catch(error => {
            console.log({ error: error })
            this.errorMessage = 'Problème de connexion'
          })
      } else {
        this.errorMessage = 'Veuillez renseigner un email et un mot de passe'
      }
    }
  }
}
</script>

<style lang="css">
.page-container-signup{
  margin-top: 10em;
}

a {
  text-decoration: none;
  color: #2c3e50 !important;
}

#create-account{
  font-weight: bold;
  margin-left: 10px;
  color: rgba(253, 45, 6, 0.8);
}

.ml-1:hover{
text-decoration: none !important;
}

#login-title{
  color: rgba(253, 45, 6, 0.8);
  font-weight: bold;
}

#card-signup{
  border-radius: 20px;
}

.account-btn {
  background-color: rgba(253, 45, 6, 0.8) !important;
  color: white !important;
  border-radius: 1rem;
  border: none;
  margin-bottom: 1rem;
}

.account-btn:hover{
  background-color: rgb(253, 45, 6) !important;
  color: white !important;
}

.account-btn:focus{
  background-color: rgb(253, 45, 6) !important;
  color: white !important;
}

.account-btn:active {
  background-color: rgb(253, 45, 6) !important;
  color: white !important;
}

@media screen and (min-width: 280px) and (max-width: 769px) {
  .card-body {
    padding: 0.7rem;
  }

  .shadow {
    box-shadow: 0rem 0.2rem 0.5rem rgba(0, 0, 0, 0.08) !important;
  }
  
  .login-text {
    font-size: 1.1rem;
  }
}
</style>