<template>
  <no-ssr>
    <div id="app">
      <nav class="navbar is-transparent" role="navigation" aria-label="main navigation">
        <div class="navbar-brand">
          <router-link to="/" exact class="navbar-item">
            <img class="logo" src="~public/logo.png" alt="logo">
          </router-link>
          <div role="button" class="navbar-burger" data-target="navbarExampleTransparentExample" aria-label="menu" aria-expanded="false">
            <span aria-hidden="true"></span>
            <span aria-hidden="true"></span>
            <span aria-hidden="true"></span>
          </div>
        </div>

        <div id="navbarExampleTransparentExample" class="navbar-menu">
          <div class="navbar-start">
            <router-link to="/" exact class="navbar-item">
              Home
            </router-link>
            <router-link to="/cart" class="navbar-item">
              Carts ({{itemCount}})
            </router-link>
          </div>
        </div>

        <div class="navbar-end">
          <div class="navbar-item">
            {{isLogged}}
          </div>
          <div class="navbar-item">
            <a class="button is-inverted" @click="logout">Logout</a>
          </div>
        </div>
      </nav>

      <transition name="fade" mode="out-in">
        <router-view class="view"></router-view>
      </transition>
      <cs-footer></cs-footer>
    </div>
  </no-ssr>
</template>

<script>
import Footer from './components/Footer.vue'
import { getUser, signoutRedirect } from './auth/usermanager'
import { getItem } from './helper/storage'
import NoSSR from 'vue-no-ssr'

export default {
  name: 'app',
  components: {
    'no-ssr': NoSSR
  },
  data() {
    return {
      userId: undefined,
      username: undefined
    }
  },
  computed: {
    itemCount() {
      var cart = this.$store.state.cart
      cart = cart || {}
      cart.items = cart.items || []
      return cart.items.length
    },
    isLogged() {
      if (this.userId) return 'Logged!'
    }
  },
  beforeMount() {
    getUser(user => {
      if (user) this.userId = user.sub
    })
    var cartId = getItem('cartId')
    if (cartId) {
      this.$store.dispatch('GET_CART')
    }
  },
  methods: {
    logout() {
      signoutRedirect(respnose => {
        this.$store.commit('LOGOUT')
      })
    }
  },
  components: {
    'cs-footer': Footer
  }
}
</script>

<style lang="stylus">
.fade-enter-active, .fade-leave-active {
  transition: all 0.2s ease;
}

.fade-enter, .fade-leave-active {
  opacity: 0;
}
</style>
