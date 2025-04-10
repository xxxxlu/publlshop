<template>
  <div class="success-page">
    <div class="page-header bg-light-gray py-4">
      <b-container>
        <h1 class="page-title">Order Confirmed</h1>
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
            <li class="breadcrumb-item active" aria-current="page">Success</li>
          </ol>
        </nav>
      </b-container>
    </div>

    <b-container class="py-4">
      <!-- No Order Warning -->
      <b-alert show variant="warning" v-if="!currentOrder">
        No order found. <router-link to="/products">Browse products</router-link> to start shopping.
      </b-alert>

      <div v-if="currentOrder">
        <div class="checkout-steps mb-4">
          <b-progress :value="3" :max="3" height="10px" class="mb-3"></b-progress>
          <div class="d-flex justify-content-between">
            <div class="step">1. Cart Summary</div>
            <div class="step">2. Payment</div>
            <div class="step active">3. Complete</div>
          </div>
        </div>

        <div class="success-container">
          <div class="success-icon">
            <b-icon icon="check-circle" font-scale="3" variant="success"></b-icon>
          </div>

          <h2 class="success-title">Thank You for Your Order!</h2>

          <p class="success-message">
            Your order has been placed successfully. We'll process it as soon as possible.
            You will receive a confirmation email with your order details.
          </p>

          <div class="order-details">
            <div class="order-detail-item">
              <span class="detail-label">Order Number:</span>
              <span class="detail-value">{{ currentOrder.id }}</span>
            </div>

            <div class="order-detail-item">
              <span class="detail-label">Order Date:</span>
              <span class="detail-value">{{ formatDate(currentOrder.date) }}</span>
            </div>

            <div class="order-detail-item">
              <span class="detail-label">Order Total:</span>
              <span class="detail-value">Rs. {{ (currentOrder.total + currentOrder.shippingCost).toLocaleString() }}</span>
            </div>

            <div class="order-detail-item">
              <span class="detail-label">Payment Method:</span>
              <span class="detail-value">{{ getPaymentMethod() }}</span>
            </div>
          </div>

          <div class="shipping-details mt-4">
            <h5>Shipping Address</h5>
            <address>
              {{ shippingAddress.name }}<br>
              {{ shippingAddress.address }}<br>
              {{ shippingAddress.city }}, {{ shippingAddress.province }} {{ shippingAddress.postalCode }}<br>
              <strong>Phone:</strong> {{ currentOrder.shippingDetails.phone }}
            </address>
          </div>

          <div class="order-items mt-4">
            <h5>Order Items</h5>
            <b-table
              :items="currentOrder.items"
              :fields="orderItemFields"
              responsive
              class="order-items-table"
            >
              <template #cell(name)="data">
                <div class="d-flex align-items-center">
                  <b-img :src="data.item.image" :alt="data.item.name" fluid width="50" class="mr-3"></b-img>
                  {{ data.item.name }}
                </div>
              </template>

              <template #cell(price)="data">
                Rs. {{ data.item.price.toLocaleString() }}
              </template>

              <template #cell(total)="data">
                Rs. {{ (data.item.price * data.item.quantity).toLocaleString() }}
              </template>
            </b-table>
          </div>

          <div class="order-totals mt-4">
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

          <div class="d-flex justify-content-center mt-5">
            <b-button variant="primary" to="/" class="mx-2">
              Continue Shopping
            </b-button>
            <b-button variant="outline-secondary" @click="printOrder" class="mx-2">
              <b-icon icon="printer" class="mr-1"></b-icon>
              Print Order
            </b-button>
          </div>
        </div>
      </div>
    </b-container>
  </div>
</template>

<script>
import { mapState } from 'vuex'

export default {
  name: 'Success',
  data() {
    return {
      orderItemFields: [
        { key: 'name', label: 'Product' },
        { key: 'quantity', label: 'Quantity' },
        { key: 'price', label: 'Price' },
        { key: 'total', label: 'Total' }
      ]
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
    formatDate(dateString) {
      if (!dateString) return ''

      const date = new Date(dateString)
      return new Intl.DateTimeFormat('en-PK', {
        year: 'numeric',
        month: 'long',
        day: 'numeric',
        hour: '2-digit',
        minute: '2-digit'
      }).format(date)
    },
    getPaymentMethod() {
      // In a real app, this would come from the order data
      // For this demo, we'll return a default value
      return 'Cash on Delivery'
    },
    printOrder() {
      window.print()
    }
  },
  created() {
    // Redirect to home if no current order
    if (!this.currentOrder) {
      this.$router.push('/')
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

.success-container {
  background: white;
  border-radius: 8px;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
  padding: 2rem;
  text-align: center;

  .success-icon {
    margin-bottom: 1.5rem;
    color: #27ae60;
  }

  .success-title {
    font-size: 1.8rem;
    font-weight: 600;
    margin-bottom: 1rem;
  }

  .success-message {
    color: #666;
    max-width: 600px;
    margin: 0 auto 2rem;
  }
}

.order-details {
  display: inline-block;
  text-align: left;
  padding: 1.5rem;
  background-color: #f8f9fa;
  border-radius: 8px;
  margin-bottom: 2rem;

  .order-detail-item {
    margin-bottom: 0.5rem;

    &:last-child {
      margin-bottom: 0;
    }

    .detail-label {
      font-weight: 600;
      margin-right: 0.5rem;
    }
  }
}

.shipping-details, .order-items, .order-totals {
  text-align: left;

  h5 {
    font-size: 1.1rem;
    font-weight: 600;
    margin-bottom: 1rem;
    border-bottom: 1px solid #eee;
    padding-bottom: 0.5rem;
  }

  address {
    color: #666;
    line-height: 1.5;
  }
}

.order-items-table {
  img {
    max-height: 50px;
    object-fit: contain;
  }
}

.order-totals {
  margin-left: auto;
  max-width: 300px;
}

@media print {
  .page-header, .checkout-steps, .success-icon, .action-buttons {
    display: none !important;
  }

  .success-container {
    box-shadow: none;
    padding: 0;
  }
}
</style>
