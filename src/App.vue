<template>
  <div id="app">
    <header>
      <b-navbar toggleable="lg" type="light" variant="light">
        <b-container>
          <b-navbar-brand to="/" class="d-flex align-items-center">
            <img src="@/assets/logo.svg" alt="publlshop" height="40" class="logo-animation">
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

    <footer class="footer py-4 mt-auto">
      <!-- Decorative wave for the footer top --> 
      <div class="footer-wave">
        <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 1440 100" preserveAspectRatio="none">
          <path fill="#e6f7ff" d="M0,50 C150,10 300,80 450,50 C600,20 750,70 900,40 C1050,10 1200,50 1350,30 L1440,0 L1440,100 L0,100 Z"></path>
        </svg>
      </div>
      <div class="container footer-content">
        <div class="row">
          <div class="col-md-4 mb-4 mb-md-0">
            <img src="@/assets/logo.svg" alt="publlshop" height="40" class="mb-3">
            <p>Quality products at affordable prices delivered to your doorstep.</p>
          </div>
          <div class="col-md-4 mb-4 mb-md-0">
            <h5>Contact Us</h5>
            <p><b-icon icon="envelope-fill" class="mr-2"></b-icon> publlshop@publlshop.fun</p>
          </div>
          <div class="col-md-4">
            <h5>Payment Methods</h5>
            <div class="payment-methods">
              <span class="payment-icon"><img src="@/assets/easypaisa.png" alt="" height="30" /></span> <!-- Easypaisa -->
              <span class="payment-icon"><img src="@/assets/jazzcash.png" alt="" height="30" /></span> <!-- JazzCash -->
            </div>
          </div>
        </div>
        <div class="text-center mt-4 pt-3 border-top">
          <p>Copyright © {{ new Date().getFullYear() }} Publlshop Store. Powered by VertexFlow Technologies (SMC-Private ) limited</p>
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

.text-secondary {
  color: #2c3e50 !important;
}

/* Logo animations */
.logo-animation {
  transition: transform 0.3s ease;
}

.logo-animation:hover {
  transform: scale(1.05);
}

/* Footer styling */
.footer {
  background-color: #f8f9fa;
  position: relative;
}

.footer-wave {
  position: absolute;
  top: -50px;
  left: 0;
  width: 100%;
  height: 50px;
  overflow: hidden;
}

.footer-wave svg {
  width: 100%;
  height: 100%;
}

.footer-content {
  position: relative;
  z-index: 1;
}

.payment-methods {
  display: flex;
  gap: 10px;
  margin-top: 10px;
}

.payment-icon {
  display: inline-flex;
  align-items: center;
  justify-content: center;
  width: 40px;
  height: 40px;
  background-color: #fff;
  border-radius: 5px;
  box-shadow: 0 2px 5px rgba(0,0,0,0.1);
  font-weight: bold;
  transition: transform 0.2s ease;
}

.payment-icon:hover {
  transform: translateY(-3px);
}

.social-icons {
  display: flex;
  gap: 15px;
  margin-top: 10px;
}

.social-icon {
  display: inline-flex;
  align-items: center;
  justify-content: center;
  width: 36px;
  height: 36px;
  background-color: #3498db;
  color: white;
  border-radius: 50%;
  transition: all 0.3s ease;
}

.social-icon:hover {
  background-color: #2c3e50;
  transform: translateY(-3px);
}
</style>
