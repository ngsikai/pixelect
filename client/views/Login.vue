<template>
  <div class="login">
    <video class="bg" preload="auto" autoplay="true" loop="loop" muted="muted" volume="0">
      <source src="/assets/bg.mp4" type="video/mp4" />
      <source src="/assets/bg.webm" type="video/webm" />
    </video>
    <section class="section">
      <div class="container has-text-centered">
          <img class="logo" src="/assets/logo.svg"/>
          <h1 class="title">Pixelect</h1>
          <fb-signin-button
            :params="fbSignInParams"
            @success="onSignInSuccess"
            @error="onSignInError">
            Sign in with Facebook
          </fb-signin-button>
      </div>
    </section>
  </div>
</template>

<script>
import store, { SET_LOGIN_STATE } from '@/store'

export default {
  data() {
    return {
      fbSignInParams: {
        scope: '',
        return_scopes: true,
      },
    }
  },
  computed: {
    isLoggedIn() {
      return store.state.isLoggedIn
    },
  },
  methods: {
    onSignInSuccess(response) {
      FB.api('/me', dude => {
        console.log(`Good to see you, ${dude.name}.`)
        store.dispatch(SET_LOGIN_STATE, true)
        fetch('/users', {
          headers: {
            Accept: 'application/json',
            'Content-Type': 'application/json',
          },
          method: 'POST',
          body: JSON.stringify({ userId: dude.id, name: dude.name }),
        })
      })
      FB.getLoginStatus(response => {
        if (response.status === 'connected') {
          // this.accessToken = response.authResponse.accessToken
          console.log('successfully connected!')
          console.log(response)
        } else {
          console.log('failed to connect')
        }
      })
    },
    onSignInError(error) {
      console.log('OH NOES', error)
    },
  },
}
</script>

<style scoped>
.login {
  display: flex;
  flex-direction: column;
  align-content: center;
  justify-content: center;
  width: 100vw;
  height: 100vh;
}

.logo {
  height: 300px;
  margin: 0 50px 50px 0;
}

.title {
  font-size: 50px;
  color: #fff;
}

.fb-signin-button {
  /* This is where you control how the button looks. Be creative! */
  display: inline-block;
  padding: 10px 20px;
  border-radius: 3px;
  background-color: #4267b2;
  color: #fff;
  cursor: pointer;
  font-size: 20px;
  margin: 20px 0 50px 0;
}

.bg {
  position: fixed;
  top: 50%;
  left: 50%;
  min-width: 100%;
  min-height: 100%;
  width: auto;
  height: auto;
  z-index: 0;
  transform: translate(-50%, -50%);
  object-fit: cover;
}
</style>