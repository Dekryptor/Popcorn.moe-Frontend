<template>
    <div>
        <v-btn fab @click.stop="$router.go(-1)" class="button-float main-color" small>
            <v-icon>arrow_back</v-icon>
        </v-btn>
        <main>
            <div class="auth-root">
                <v-fade-transition mode="out-in">
                    <router-view class="auth-center"></router-view>
                </v-fade-transition>
            </div>
        </main>
    </div>
</template>

<script>
import { VBtn, VIcon } from 'vuetify/es5/components'
import { VFadeTransition } from 'vuetify/es5/components/transitions'

export default {
  components: {
    VBtn,
    VIcon,
    VFadeTransition
  }
}
</script>

<style lang="stylus">
  @import '../../stylus/main'

  .button-float {
    position: fixed !important;
    z-index: 1000;
  }
  .auth-root {
      width: 100vw;
      height: 100vh;
      background: url(/static/auth-background.jpg) no-repeat;
      background-size: cover;
  }
  .auth-center {
    position: absolute;
    left: 50%;
    top: 50%;
    transform: translate(-50%, -50%);
  }
</style>
