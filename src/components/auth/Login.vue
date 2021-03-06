<template>
    <div class="elevation-2">
        <v-layout row wrap>
            <v-flex xs4 class="white">
                <h1 class="buttons-title">Se connecter avec :</h1>
                <v-btn class="social-button google-color" large light @click.stop="login('google')">
                  <img src="/static/icons/google-icon.svg">
                  Google
                </v-btn>
                <v-btn class="social-button discord-color" large light @click.stop="login('discord')">
                  <img src="/static/icons/discord-icon.svg">
                  Discord
                </v-btn>
                <v-btn class="social-button twitter-color" large light @click.stop="login('twitter')">
                  <img src="/static/icons/twitter-icon.svg">
                  Twitter
                </v-btn>
                <v-btn class="social-button kitsu-color" large light @click.stop="login('kitsu')">
                  <img src="/static/icons/kitsu-icon.svg">
                  Kitsu
                </v-btn>
            </v-flex>
            <v-flex class="fields-container" xs8>
              <div class="inputs">
                <div class="text-xs-center">
                    <object data="/static/logo-animated.svg" type="image/svg+xml" class="auth-logo"></object>
                </div>
                <v-alert
                  :info="alert && alert.info"
                  :error="alert && alert.error"
                  :success="alert && alert.success"
                  :warning="alert && alert.warning"
                  dismissible
                  :value="alert !== null"
                  @input="alert = null">
                  {{ alert && alert.text }}
                </v-alert>
                <v-text-field label="E-mail / Pseudo" light
                  v-model="username"
                ></v-text-field>
                <v-text-field label="Mot de passe" light
                  v-model="password"
                  :append-icon="hidePassword ? 'visibility' : 'visibility_off'"
                  :append-icon-cb="() => (hidePassword = !hidePassword)"
                  :type="hidePassword ? 'password' : 'text'"
                ></v-text-field>
                <div class="text-xs-right">
                  <v-btn class="login-button secondary-color black--text" large light @click.stop="login()">Se Connecter</v-btn>
                </div>
              </div>
              <div class="link-container">
                <router-link replace :to="{ name: 'SignUp' }">Se créer un compte</router-link>
              </div>
            </v-flex>

        </v-layout>
    </div>
</template>

<script>
  import { VBtn, VTextField, VIcon, VAlert } from 'vuetify/es5/components'
  import { VContainer, VFlex, VLayout } from 'vuetify/es5/components/VGrid'
  import { login } from '../../utils/auth'
  import { mapActions } from 'vuex'
  
  export default {
    data() {
        return {
            username: '',
            password: '',
            hidePassword: true,
            alert: null
        }
    },
    components: {
      VAlert,
      VBtn,
      VTextField,
      VContainer,
      VFlex,
      VLayout
    },
    methods: {
        ...mapActions({
          setIsAuth: 'setIsAuth'
        }),
        login(provider) {
            if (provider) {
                const callback = encodeURIComponent(`${location.origin}/#${this.$router.last}`)
                window.location.assign(`${process.env.AUTH_URL}/login/${provider}?callback=${callback}`)
            } else {
                login(this.username, this.password).then(() => {
                  this.$router.go(-1)
                  this.setIsAuth(true)
                }).catch(error => {
                  if (error.alert)
                    this.alert = error.alert
                  else
                    return Promise.reject(error)
                })
            }
        }
    }
  }
</script>

<style lang="stylus">
  @import '../../stylus/main.styl';

  .buttons-title {
    padding-top: 30px;
    text-align: center;
    color: #212121 !important;
    font-size: 20px;
  }

  .social-button {
    .btn__content img {
        position: absolute;
        left: 10px;
        height: 35px;
    }
    width: calc(100% - 16px);
  }

  .fields-container {

    background-color: rgba(255, 255, 255, 0.71);
    .inputs{
      padding-top: 15px;
      padding-bottom: 15px;
      padding-left: 50px !important;
      padding-right: 50px !important;
    }

    .link-container {
      width: 100%;
      padding: 15px;

      a {
        text-decoration: none;
        color: #4b4b4b !important;
      }
    }
  }

  .login-button {
    margin: 0 !important;
  }

  .auth-logo {
    width: 110px;
  }
</style>
