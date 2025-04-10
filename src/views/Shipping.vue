<template>
  <div class="shipping-page">
    <div class="page-header bg-light-gray py-4">
      <b-container>
        <h1 class="page-title">Payment Method</h1>
        <nav aria-label="breadcrumb">
          <ol class="breadcrumb">
            <li class="breadcrumb-item">
              <router-link to="/">Home</router-link>
            </li>
            <li class="breadcrumb-item">
              <router-link to="/cart">Cart</router-link>
            </li>
            <li class="breadcrumb-item">
              <router-link to="/checkout">Checkout</router-link>
            </li>
            <li class="breadcrumb-item active" aria-current="page">Payment</li>
          </ol>
        </nav>
      </b-container>
    </div>

    <b-container class="py-4">
      <!-- No Order Warning -->
      <b-alert show variant="warning" v-if="!currentOrder">
        No active order found. <router-link to="/cart">Return to cart</router-link> to start the checkout process.
      </b-alert>

      <div v-if="currentOrder">
        <div class="checkout-steps mb-4">
          <b-progress :value="2" :max="3" height="10px" class="mb-3"></b-progress>
          <div class="d-flex justify-content-between">
            <div class="step">1. Cart Summary</div>
            <div class="step active">2. Payment</div>
            <div class="step">3. Complete</div>
          </div>
        </div>

        <b-row>
          <!-- Payment Methods -->
          <b-col lg="8" order="2" order-lg="1" class="mb-4">
            <div class="checkout-section">
              <h2 class="checkout-section-title">Select Payment Method</h2>

              <b-form @submit.prevent="processPayment">
                <div class="payment-methods">
                  <div
                    v-for="method in paymentMethods"
                    :key="method.id"
                    class="payment-method-option"
                    :class="{ 'active': selectedPaymentMethod === method.id }"
                    @click="selectedPaymentMethod = method.id"
                  >
                    <div class="payment-method-radio">
                      <b-form-radio
                        v-model="selectedPaymentMethod"
                        :value="method.id"
                        name="payment-method"
                      ></b-form-radio>
                    </div>
                    <div class="payment-logo">
                      <img v-if="method.image" :src="method.image" :alt="method.name" />
                      <b-icon v-else :icon="method.icon" font-scale="1.5"></b-icon>
                    </div>
                    <div class="payment-method-details">
                      <div class="payment-method-name">{{ method.name }}</div>
                      <div class="payment-method-description text-muted small">{{ method.description }}</div>
                    </div>
                  </div>
                </div>

                <!-- JazzCash Form -->
                <div v-if="selectedPaymentMethod === 'jazzcash'" class="payment-form mt-4">
                  <h5>JazzCash Payment</h5>
                  <b-form-group label="JazzCash Mobile Number" label-for="jazzcash-number">
                    <b-form-input
                      id="jazzcash-number"
                      v-model="paymentDetails.jazzcash.mobileNumber"
                      placeholder="Enter JazzCash mobile number"
                      required
                    ></b-form-input>
                  </b-form-group>
                  <b-form-group label="CNIC (Last 6 digits)" label-for="jazzcash-cnic">
                    <b-form-input
                      id="jazzcash-cnic"
                      v-model="paymentDetails.jazzcash.cnic"
                      placeholder="Enter last 6 digits of CNIC"
                      required
                      maxlength="6"
                    ></b-form-input>
                  </b-form-group>
                </div>

                <!-- EasyPaisa Form -->
                <div v-if="selectedPaymentMethod === 'easypaisa'" class="payment-form mt-4">
                  <h5>EasyPaisa Payment</h5>
                  <b-form-group label="EasyPaisa Mobile Number" label-for="easypaisa-number">
                    <b-form-input
                      id="easypaisa-number"
                      v-model="paymentDetails.easypaisa.mobileNumber"
                      placeholder="Enter EasyPaisa mobile number"
                      required
                    ></b-form-input>
                  </b-form-group>
                  <b-form-group label="Email (for receipt)" label-for="easypaisa-email">
                    <b-form-input
                      id="easypaisa-email"
                      v-model="paymentDetails.easypaisa.email"
                      type="email"
                      placeholder="Enter email for receipt"
                      required
                    ></b-form-input>
                  </b-form-group>
                </div>

                <div class="form-actions d-flex justify-content-between mt-4">
                  <b-button variant="outline-secondary" to="/checkout">
                    <b-icon icon="arrow-left" class="mr-1"></b-icon>
                    Back to Shipping
                  </b-button>

                  <b-button variant="success" type="submit" :disabled="!selectedPaymentMethod">
                    Complete Order
                    <b-icon icon="check2" class="ml-1"></b-icon>
                  </b-button>
                </div>
              </b-form>
            </div>
          </b-col>

          <!-- Order Summary -->
          <b-col lg="4" order="1" order-lg="2" class="mb-4">
            <div class="order-summary">
              <h2 class="checkout-section-title">Order Summary</h2>

              <div class="order-details">
                <div class="order-id mb-3">
                  <strong>Order ID:</strong> {{ currentOrder.id }}
                </div>

                <div class="order-shipping-info mb-3">
                  <h5>Shipping Address</h5>
                  <address class="mb-0">
                    {{ shippingAddress.name }}<br>
                    {{ shippingAddress.address }}<br>
                    {{ shippingAddress.city }}, {{ shippingAddress.province }} {{ shippingAddress.postalCode }}<br>
                    <strong>Phone:</strong> {{ currentOrder.shippingDetails.phone }}
                  </address>
                </div>

                <hr />

                <div class="order-items-count mb-3">
                  <strong>Items:</strong> {{ currentOrder.items.length }} item(s)
                </div>

                <div class="order-summary-totals">
                  <div class="d-flex justify-content-between mb-2">
                    <span>Subtotal:</span>
                    <span>Rs. {{ currentOrder.total.toLocaleString() }}</span>
                  </div>

                  <div class="d-flex justify-content-between mb-2">
                    <span>Shipping:</span>
                    <span>{{ currentOrder.shippingCost > 0 ? 'Rs. ' + currentOrder.shippingCost.toLocaleString() : 'Free' }}</span>
                  </div>

                  <hr />

                  <div class="d-flex justify-content-between mb-2 font-weight-bold">
                    <span>Total:</span>
                    <span>Rs. {{ (currentOrder.total + currentOrder.shippingCost).toLocaleString() }}</span>
                  </div>
                </div>
              </div>
            </div>
          </b-col>
        </b-row>
      </div>
    </b-container>
  </div>
</template>

<script>
import { mapState, mapActions } from 'vuex'
import JazzCash from '../assets/jazzcash.png'
import EasyPaisa from '../assets/easypaisa.png'

export default {
  name: 'Shipping',
  data() {
    return {
      selectedPaymentMethod: null,
      paymentMethods: [
        {
          id: 'jazzcash',
          name: 'JazzCash',
          description: 'Pay with your JazzCash mobile account',
          image: JazzCash
        },
        {
          id: 'easypaisa',
          name: 'EasyPaisa',
          description: 'Pay with your EasyPaisa mobile account',
          image: EasyPaisa
        }
      ],
      paymentDetails: {
        jazzcash: {
          mobileNumber: '',
          cnic: ''
        },
        easypaisa: {
          mobileNumber: '',
          email: ''
        }
      }
    }
  },
  computed: {
    ...mapState(['currentOrder']),
    shippingAddress() {
      if (!this.currentOrder) return {}

      const details = this.currentOrder.shippingDetails
      return {
        name: `${details.firstName} ${details.lastName}`,
        address: details.address,
        city: details.city,
        province: details.province,
        postalCode: details.postalCode
      }
    }
  },
  methods: {
    ...mapActions(['completeOrder']),
    processPayment() {
      // In a real implementation, this would handle payment processing
      // For this demo, we'll just simulate a successful payment

      // Add a loading state
      this.loading = true

      // Simulate API call to payment gateway
      setTimeout(() => {
        this.completeOrder()
        this.$router.push('/checkout/success')
      }, 1500)
    }
  },
  created() {
    // Redirect to checkout if no current order
    if (!this.currentOrder) {
      this.$router.push('/checkout')
    }

    // Pre-fill customer email if available
    if (this.currentOrder && this.currentOrder.shippingDetails.email) {
      this.paymentDetails.easypaisa.email = this.currentOrder.shippingDetails.email
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

.payment-method-option {
  display: flex;
  align-items: center;
  padding: 1rem;
  border: 1px solid #e9ecef;
  border-radius: 8px;
  margin-bottom: 1rem;
  cursor: pointer;
  transition: all 0.2s;

  &:hover, &.active {
    border-color: #3498db;
    background-color: rgba(52, 152, 219, 0.05);
  }

  .payment-method-radio {
    margin-right: 1rem;
  }

  .payment-logo {
    display: flex;
    align-items: center;
    justify-content: center;
    width: 50px;
    height: 40px;
    margin-right: 1rem;

    img {
      max-width: 100%;
      max-height: 100%;
      object-fit: contain;
    }
  }

  .payment-method-name {
    font-weight: 600;
    margin-bottom: 0.25rem;
  }
}

.order-shipping-info {
  h5 {
    font-size: 1.1rem;
    font-weight: 600;
    margin-bottom: 0.5rem;
  }

  address {
    color: #666;
    line-height: 1.5;
  }
}
</style>
