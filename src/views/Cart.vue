<template>
  <div class="cart-page">
    <div class="page-header bg-light-gray py-4">
      <b-container>
        <h1 class="page-title">Your Shopping Cart</h1>
        <nav aria-label="breadcrumb">
          <ol class="breadcrumb">
            <li class="breadcrumb-item">
              <router-link to="/">Home</router-link>
            </li>
            <li class="breadcrumb-item active" aria-current="page">Cart</li>
          </ol>
        </nav>
      </b-container>
    </div>

    <b-container class="py-4">
      <b-row v-if="cart.length > 0">
        <!-- Cart Items -->
        <b-col lg="8" class="mb-4 mb-lg-0">
          <div class="cart-items-container">
            <h2 class="cart-section-title">Cart Items ({{ cart.length }})</h2>

            <div class="cart-items">
              <b-card v-for="item in cart" :key="item.id + Math.random()" class="cart-item mb-3">
                <b-row>
                  <b-col sm="3" class="mb-3 mb-sm-0">
                    <router-link :to="`/product/${item.id}`">
                      <b-img :src="item.image" :alt="item.name" fluid class="cart-item-image"></b-img>
                    </router-link>
                  </b-col>
                  <b-col sm="9">
                    <div class="d-flex justify-content-between align-items-start">
                      <router-link :to="`/product/${item.id}`" class="cart-item-title">
                        {{ item.name }}
                      </router-link>
                      <b-button
                        variant="link"
                        class="text-danger p-0"
                        title="Remove item"
                        @click="removeFromCart(item.id)"
                      >
                        <b-icon icon="trash"></b-icon>
                      </b-button>
                    </div>

                    <div class="cart-item-price mt-2">
                      Rs. {{ item.price.toLocaleString() }}
                    </div>

                    <div class="d-flex justify-content-between align-items-center mt-3">
                      <div class="cart-item-quantity">
                        <b-input-group style="width: 130px">
                          <b-input-group-prepend>
                            <b-button
                              variant="outline-secondary"
                              @click="decrementQuantity(item)"
                              :disabled="item.quantity <= 1"
                            >
                              <b-icon icon="dash"></b-icon>
                            </b-button>
                          </b-input-group-prepend>

                          <b-form-input
                            v-model="item.quantity"
                            type="number"
                            min="1"
                            class="text-center"
                            @change="updateCartItemQuantity({ productId: item.id, quantity: parseInt(item.quantity) })"
                          ></b-form-input>

                          <b-input-group-append>
                            <b-button
                              variant="outline-secondary"
                              @click="incrementQuantity(item)"
                            >
                              <b-icon icon="plus"></b-icon>
                            </b-button>
                          </b-input-group-append>
                        </b-input-group>
                      </div>

                      <div class="cart-item-total font-weight-bold">
                        Rs. {{ (item.price * item.quantity).toLocaleString() }}
                      </div>
                    </div>
                  </b-col>
                </b-row>
              </b-card>
            </div>

            <div class="cart-actions mt-4">
              <b-button variant="outline-primary" to="/products">
                <b-icon icon="arrow-left" class="mr-1"></b-icon>
                Continue Shopping
              </b-button>
              <b-button variant="danger" @click="confirmClearCart" class="ml-2">
                <b-icon icon="trash" class="mr-1"></b-icon>
                Clear Cart
              </b-button>
            </div>
          </div>
        </b-col>

        <!-- Cart Summary -->
        <b-col lg="4">
          <div class="cart-summary">
            <h2 class="cart-section-title">Order Summary</h2>

            <div class="summary-detail d-flex justify-content-between mb-2">
              <span>Subtotal:</span>
              <span>Rs. {{ cartTotal.toLocaleString() }}</span>
            </div>

            <div class="summary-detail d-flex justify-content-between mb-2">
              <span>Shipping:</span>
              <span>{{ shippingCost > 0 ? 'Rs. ' + shippingCost.toLocaleString() : 'Free' }}</span>
            </div>

            <hr />

            <div class="summary-total d-flex justify-content-between mb-3">
              <span class="font-weight-bold">Total:</span>
              <span class="font-weight-bold">Rs. {{ (cartTotal + shippingCost).toLocaleString() }}</span>
            </div>

            <b-button
              variant="success"
              block
              size="lg"
              to="/checkout"
              :disabled="cart.length === 0"
            >
              Proceed to Checkout
            </b-button>

            <div class="payment-methods mt-4">
              <div class="d-flex justify-content-center">
                <div class="payment-method-icon">
                  <img src="../assets/jazzcash.png" alt="JazzCash" height="30" />
                </div>
                <div class="payment-method-icon ml-3">
                  <img src="../assets/easypaisa.png" alt="EasyPaisa" height="30" />
                </div>
                <div class="payment-method-icon ml-3">
                  <b-icon icon="cash" font-scale="1.5"></b-icon>
                </div>
              </div>
              <p class="text-center text-muted small mt-2">
                Secure Payment Options
              </p>
            </div>
          </div>
        </b-col>
      </b-row>

      <!-- Empty Cart Message -->
      <div v-else class="empty-cart-container text-center py-5">
        <b-icon icon="cart-x" font-scale="5" class="text-muted mb-3"></b-icon>
        <h3>Your cart is empty</h3>
        <p class="text-muted">Looks like you haven't added any products to your cart yet.</p>
        <b-button variant="primary" to="/products" class="mt-3">
          Start Shopping
        </b-button>
      </div>
    </b-container>
  </div>
</template>

<script>
import { mapState, mapGetters, mapActions } from 'vuex'

export default {
  name: 'Cart',
  data() {
    return {
      shippingCost: 0
    }
  },
  computed: {
    ...mapState(['cart']),
    ...mapGetters(['cartTotal']),
    isEmpty() {
      return this.cart.length === 0
    }
  },
  methods: {
    ...mapActions(['removeFromCart', 'updateCartItemQuantity', 'clearCart']),
    incrementQuantity(item) {
      const newQuantity = item.quantity + 1
      this.updateCartItemQuantity({
        productId: item.id,
        quantity: newQuantity
      })
    },
    decrementQuantity(item) {
      if (item.quantity > 1) {
        const newQuantity = item.quantity - 1
        this.updateCartItemQuantity({
          productId: item.id,
          quantity: newQuantity
        })
      }
    },
    confirmClearCart() {
      this.$bvModal.msgBoxConfirm('Are you sure you want to clear your cart?', {
        title: 'Clear Cart',
        okVariant: 'danger',
        okTitle: 'Yes, clear cart',
        cancelTitle: 'Cancel',
        centered: true
      }).then(value => {
        if (value) {
          this.clearCart()
          this.$bvToast.toast('Your cart has been cleared', {
            title: 'Cart Cleared',
            variant: 'success',
            toaster: 'b-toaster-bottom-right',
            autoHideDelay: 3000
          })
        }
      })
    }
  },
  watch: {
    cartTotal(newTotal) {
      // Free shipping for orders above Rs. 2000
      this.shippingCost = newTotal >= 2000 ? 0 : 200
    }
  },
  created() {
    // Calculate shipping cost on page load
    if (this.cartTotal >= 2000) {
      this.shippingCost = 0
    } else {
      this.shippingCost = 200
    }
  }
}
</script>

<style lang="scss" scoped>
.page-header {
  background-color: #f8f9fa;

  .page-title {
    font-size: 2rem;
    font-weight: 600;
    margin-bottom: 0.5rem;
  }

  .breadcrumb {
    background: transparent;
    padding: 0;
    margin: 0;

    a {
      color: #3498db;
      text-decoration: none;

      &:hover {
        text-decoration: underline;
      }
    }
  }
}

.cart-section-title {
  font-size: 1.5rem;
  font-weight: 600;
  margin-bottom: 1.5rem;
  position: relative;

  &:after {
    content: '';
    position: absolute;
    bottom: -0.5rem;
    left: 0;
    width: 50px;
    height: 2px;
    background-color: #3498db;
  }
}

.cart-items-container {
  background: white;
  border-radius: 8px;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
  padding: 1.5rem;
}

.cart-item {
  &-image {
    max-height: 100px;
    object-fit: contain;
  }

  &-title {
    font-weight: 600;
    color: #333;
    text-decoration: none;
    font-size: 1.1rem;

    &:hover {
      color: #3498db;
      text-decoration: none;
    }
  }

  &-price {
    color: #7f8c8d;
    font-size: 0.95rem;
  }

  &-total {
    color: #2c3e50;
    font-size: 1.1rem;
  }
}

.cart-summary {
  background: white;
  border-radius: 8px;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
  padding: 1.5rem;

  .summary-detail {
    font-size: 1rem;
    color: #666;
  }

  .summary-total {
    font-size: 1.2rem;
    color: #2c3e50;
  }
}

.payment-methods {
  .payment-method-icon {
    display: flex;
    align-items: center;
    justify-content: center;
    padding: 0.5rem;

    img {
      max-height: 30px;
    }
  }
}

.empty-cart-container {
  background: white;
  border-radius: 8px;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
  padding: 3rem;

  h3 {
    font-weight: 600;
    margin-bottom: 1rem;
  }
}
</style>
