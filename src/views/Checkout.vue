<template>
  <div class="checkout-page">
    <div class="page-header bg-light-gray py-4">
      <b-container>
        <h1 class="page-title">Checkout</h1>
        <nav aria-label="breadcrumb">
          <ol class="breadcrumb">
            <li class="breadcrumb-item">
              <router-link to="/">Home</router-link>
            </li>
            <li class="breadcrumb-item">
              <router-link to="/cart">Cart</router-link>
            </li>
            <li class="breadcrumb-item active" aria-current="page">Checkout</li>
          </ol>
        </nav>
      </b-container>
    </div>

    <b-container class="py-4">
      <!-- Empty Cart Warning -->
      <b-alert show variant="warning" v-if="cart.length === 0">
        Your cart is empty. <router-link to="/products">Browse products</router-link> to add items to your cart.
      </b-alert>

      <div v-if="cart.length > 0">
        <div class="checkout-steps mb-4">
          <b-progress :value="1" :max="3" height="10px" class="mb-3"></b-progress>
          <div class="d-flex justify-content-between">
            <div class="step active">1. Cart Summary</div>
            <div class="step">2. Payment</div>
            <div class="step">3. Complete</div>
          </div>
        </div>

        <b-row>
          <!-- Shipping Details Form -->
          <b-col lg="8" order="2" order-lg="1" class="mb-4">
            <div class="checkout-section">
              <h2 class="checkout-section-title">Shipping Details</h2>

              <b-form @submit.prevent="proceedToPayment" class="checkout-form">
                <b-row>
                  <b-col md="6">
                    <b-form-group label="First Name" label-for="firstName">
                      <b-form-input
                        id="firstName"
                        v-model="form.firstName"
                        placeholder="Enter first name"
                        required
                      ></b-form-input>
                    </b-form-group>
                  </b-col>

                  <b-col md="6">
                    <b-form-group label="Last Name" label-for="lastName">
                      <b-form-input
                        id="lastName"
                        v-model="form.lastName"
                        placeholder="Enter last name"
                        required
                      ></b-form-input>
                    </b-form-group>
                  </b-col>
                </b-row>

                <b-form-group label="Email" label-for="email">
                  <b-form-input
                    id="email"
                    v-model="form.email"
                    type="email"
                    placeholder="Enter email"
                    required
                  ></b-form-input>
                </b-form-group>

                <b-form-group label="Phone Number" label-for="phone">
                  <b-form-input
                    id="phone"
                    v-model="form.phone"
                    placeholder="Enter phone number"
                    required
                  ></b-form-input>
                </b-form-group>

                <b-form-group label="Address" label-for="address">
                  <b-form-input
                    id="address"
                    v-model="form.address"
                    placeholder="Enter street address"
                    required
                  ></b-form-input>
                </b-form-group>

                <b-row>
                  <b-col md="6">
                    <b-form-group label="City" label-for="city">
                      <b-form-input
                        id="city"
                        v-model="form.city"
                        placeholder="Enter city"
                        required
                      ></b-form-input>
                    </b-form-group>
                  </b-col>

                  <b-col md="6">
                    <b-form-group label="Province" label-for="province">
                      <b-form-select
                        id="province"
                        v-model="form.province"
                        :options="pakistanProvinces"
                        required
                      ></b-form-select>
                    </b-form-group>
                  </b-col>
                </b-row>

                <b-form-group label="Postal Code" label-for="postalCode">
                  <b-form-input
                    id="postalCode"
                    v-model="form.postalCode"
                    placeholder="Enter postal code"
                    required
                  ></b-form-input>
                </b-form-group>

                <b-form-group>
                  <b-form-checkbox
                    id="saveInfo"
                    v-model="form.saveInfo"
                  >
                    Save this information for next time
                  </b-form-checkbox>
                </b-form-group>

                <div class="form-actions d-flex justify-content-between mt-4">
                  <b-button variant="outline-secondary" to="/cart">
                    <b-icon icon="arrow-left" class="mr-1"></b-icon>
                    Back to Cart
                  </b-button>

                  <b-button variant="primary" type="submit">
                    Continue to Payment
                    <b-icon icon="arrow-right" class="ml-1"></b-icon>
                  </b-button>
                </div>
              </b-form>
            </div>
          </b-col>

          <!-- Order Summary -->
          <b-col lg="4" order="1" order-lg="2" class="mb-4">
            <div class="order-summary">
              <h2 class="checkout-section-title">Order Summary</h2>

              <div class="order-items">
                <div
                  v-for="item in cart"
                  :key="item.id + Math.random()"
                  class="order-item d-flex py-2 border-bottom"
                >
                  <div class="order-item-image mr-3">
                    <b-img :src="item.image" :alt="item.name" fluid width="50"></b-img>
                  </div>
                  <div class="order-item-details flex-grow-1">
                    <div class="order-item-name">{{ item.name }}</div>
                    <div class="order-item-quantity text-muted small">Qty: {{ item.quantity }}</div>
                  </div>
                  <div class="order-item-price">
                    Rs. {{ (item.price * item.quantity).toLocaleString() }}
                  </div>
                </div>
              </div>

              <div class="order-summary-totals mt-4">
                <div class="d-flex justify-content-between mb-2">
                  <span>Subtotal:</span>
                  <span>Rs. {{ cartTotal.toLocaleString() }}</span>
                </div>

                <div class="d-flex justify-content-between mb-2">
                  <span>Shipping:</span>
                  <span>{{ shippingCost > 0 ? 'Rs. ' + shippingCost.toLocaleString() : 'Free' }}</span>
                </div>

                <hr />

                <div class="d-flex justify-content-between mb-2 font-weight-bold">
                  <span>Total:</span>
                  <span>Rs. {{ (cartTotal + shippingCost).toLocaleString() }}</span>
                </div>
              </div>

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
      </div>
    </b-container>
  </div>
</template>

<script>
import { mapState, mapGetters, mapActions } from 'vuex'

export default {
  name: 'Checkout',
  data() {
    return {
      form: {
        firstName: '',
        lastName: '',
        email: '',
        phone: '',
        address: '',
        city: '',
        province: null,
        postalCode: '',
        saveInfo: false
      },
      pakistanProvinces: [
        { value: null, text: 'Select Province' },
        { value: 'Punjab', text: 'Punjab' },
        { value: 'Sindh', text: 'Sindh' },
        { value: 'KPK', text: 'Khyber Pakhtunkhwa' },
        { value: 'Balochistan', text: 'Balochistan' },
        { value: 'GB', text: 'Gilgit-Baltistan' },
        { value: 'AJK', text: 'Azad Jammu & Kashmir' },
        { value: 'ICT', text: 'Islamabad Capital Territory' }
      ],
      shippingCost: 0
    }
  },
  computed: {
    ...mapState(['cart']),
    ...mapGetters(['cartTotal'])
  },
  methods: {
    ...mapActions(['createOrder']),
    proceedToPayment() {
      // Save shipping details in localStorage
      if (this.form.saveInfo) {
        localStorage.setItem('shipping_details', JSON.stringify(this.form))
      }

      // Create order and proceed to shipping page
      const orderDetails = {
        shippingDetails: { ...this.form },
        shippingCost: this.shippingCost
      }

      this.createOrder(orderDetails)
      this.$router.push('/checkout/shipping')
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

    // Redirect to cart page if cart is empty
    if (this.cart.length === 0) {
      this.$router.push('/cart')
    }

    // Load saved shipping details if available
    const savedDetails = localStorage.getItem('shipping_details')
    if (savedDetails) {
      this.form = { ...JSON.parse(savedDetails), saveInfo: true }
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

.checkout-section, .order-summary {
  background: white;
  border-radius: 8px;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
  padding: 1.5rem;
  height: 100%;
}

.checkout-section-title {
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

.checkout-steps {
  .step {
    font-weight: 500;
    color: #95a5a6;
    position: relative;

    &.active {
      color: #3498db;
      font-weight: 600;
    }
  }
}

.order-item {
  &-image {
    display: flex;
    align-items: center;
    justify-content: center;
    width: 50px;

    img {
      max-width: 100%;
      max-height: 50px;
      object-fit: contain;
    }
  }

  &-name {
    font-weight: 500;
    font-size: 0.9rem;
  }

  &-price {
    font-weight: 500;
    color: #2c3e50;
    font-size: 0.9rem;
    min-width: 80px;
    text-align: right;
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
</style>
