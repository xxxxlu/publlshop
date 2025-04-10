<template>
  <div id="app">
    <header>
      <b-navbar toggleable="lg" type="light" variant="light">
        <b-container>
          <b-navbar-brand to="/">
            <span class="text-primary">Publl</span>shop
          </b-navbar-brand>

          <b-navbar-toggle target="nav-collapse"></b-navbar-toggle>

          <b-collapse id="nav-collapse" is-nav>
            <b-navbar-nav class="mr-auto">
              <b-nav-item to="/" exact>Home</b-nav-item>
              <b-nav-item to="/products">Products</b-nav-item>
              <b-nav-item to="/contact">Contact</b-nav-item>
            </b-navbar-nav>
            
            <b-navbar-nav class="ml-auto">
              <b-nav-item to="/cart" class="cart-nav-item">
                <span class="d-flex align-items-center">
                  <b-icon icon="cart" class="mr-1"></b-icon> Cart
                  <b-badge v-if="cartItemCount > 0" variant="primary" pill class="ml-1">{{ cartItemCount }}</b-badge>
                </span>
              </b-nav-item>
            </b-navbar-nav>
          </b-collapse>
        </b-container>
      </b-navbar>
    </header>

    <main>
      <router-view/>
    </main>

    <footer class="footer bg-light py-4 mt-auto">
      <div class="container">
        <div class="text-center">
          <p>&copy; Copyright © {{ new Date().getFullYear() }}  | Powered by Data Network Solutions (SMC-Private) Limited.</p>
        </div>
      </div>
    </footer>
  </div>
</template>

<script>
import { mapGetters, mapActions } from 'vuex'

export default {
  name: 'App',
  computed: {
    ...mapGetters(['cartItemCount'])
  },
  methods: {
    ...mapActions(['initCart'])
  },
  created() {
    // 确保应用启动时加载购物车数据
    this.initCart()
  }
}
</script>

<style>
.cart-nav-item {
  font-weight: 600;
}

.navbar-light .navbar-nav .cart-nav-item.nav-item > .nav-link {
  color: #3490dc;
}

#app {
  font-family: 'Poppins', sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
  min-height: 100vh;
  display: flex;
  flex-direction: column;
}

main {
  flex: 1;
}

.text-primary {
  color: #3498db !important;
}
</style>
