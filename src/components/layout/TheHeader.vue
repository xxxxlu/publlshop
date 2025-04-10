<template>
  <header class="site-header">
    <b-navbar toggleable="md" type="light" variant="light" class="py-3">
      <b-container>
        <b-navbar-brand to="/" class="font-weight-bold">
          <span class="text-primary">Publ</span>lshop
        </b-navbar-brand>

        <b-navbar-toggle target="nav-collapse"></b-navbar-toggle>

        <b-collapse id="nav-collapse" is-nav>
          <b-navbar-nav class="ml-auto">
            <b-nav-item to="/" exact>Home</b-nav-item>
            <b-nav-item to="/products">Products</b-nav-item>
            <b-nav-item to="/contact">Contact</b-nav-item>
          </b-navbar-nav>

          <b-navbar-nav class="ml-md-3">
            <b-nav-item to="/cart" class="cart-icon">
              <b-icon icon="cart" font-scale="1.2"></b-icon>
              <span v-if="cartItemCount > 0" class="cart-badge">{{ cartItemCount }}</span>
            </b-nav-item>
          </b-navbar-nav>
        </b-collapse>
      </b-container>
    </b-navbar>
  </header>
</template>

<script>
import { mapGetters } from 'vuex'

export default {
  name: 'TheHeader',
  computed: {
    ...mapGetters(['cartItemCount'])
  }
}
</script>

<style lang="scss" scoped>
.site-header {
  box-shadow: 0 2px 10px rgba(0, 0, 0, 0.1);
}

.navbar-brand {
  font-size: 1.6rem;

  .text-primary {
    color: #3498db !important;
  }
}

.cart-icon {
  position: relative;
}

.cart-badge {
  position: absolute;
  top: 0;
  right: 0;
  background-color: #e74c3c;
  color: white;
  font-size: 10px;
  width: 18px;
  height: 18px;
  border-radius: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
  transform: translate(50%, -25%);
}
</style>
