<template>
  <div class="product-details">
    <div class="page-header bg-light-gray py-4">
      <b-container>
        <nav aria-label="breadcrumb">
          <ol class="breadcrumb">
            <li class="breadcrumb-item">
              <router-link to="/">Home</router-link>
            </li>
            <li class="breadcrumb-item">
              <router-link to="/products">Products</router-link>
            </li>
            <li class="breadcrumb-item active" aria-current="page">
              {{ product ? product.name : 'Product Details' }}
            </li>
          </ol>
        </nav>
      </b-container>
    </div>

    <b-container class="py-4">
      <b-alert show variant="danger" v-if="!product && !loading">
        Product not found. <router-link to="/products">Return to products</router-link>
      </b-alert>

      <div v-if="loading" class="text-center py-5">
        <b-spinner variant="primary" label="Loading..."></b-spinner>
        <p class="mt-3">Loading product details...</p>
      </div>

      <div v-if="product && !loading">
        <b-row>
          <!-- Product Image -->
          <b-col md="6" class="mb-4 mb-md-0">
            <div class="product-image-container">
              <b-img :src="product.image" :alt="product.name" fluid class="product-image"></b-img>
            </div>
          </b-col>

          <!-- Product Info -->
          <b-col md="6">
            <div class="product-info">
              <h1 class="product-title">{{ product.name }}</h1>
              <div class="product-price">Rs. {{ product.price.toLocaleString() }}</div>
              <div class="product-category mt-2">
                <b-badge variant="light">{{ product.category }}</b-badge>
              </div>

              <div class="product-description mt-4">
                <h5>Description</h5>
                <p>{{ product.description }}</p>
              </div>

              <div class="product-inventory mt-4">
                <div class="stock-status mb-2" :class="{ 'in-stock': product.stock > 0, 'out-of-stock': product.stock === 0 }">
                  <b-icon
                    :icon="product.stock > 0 ? 'check-circle' : 'x-circle'"
                    class="mr-2"
                  ></b-icon>
                  {{ product.stock > 0 ? 'In Stock' : 'Out of Stock' }}
                </div>
                <div v-if="product.stock > 0" class="stock-quantity">
                  <small>Only {{ product.stock }} left</small>
                </div>
              </div>

              <div class="product-actions mt-4">
                <b-form-group label="Quantity" label-for="quantity-input">
                  <b-form-spinbutton
                    id="quantity-input"
                    v-model="quantity"
                    min="1"
                    :max="product.stock"
                    class="mb-3"
                    style="width: 120px;"
                  ></b-form-spinbutton>
                </b-form-group>

                <div class="d-flex flex-wrap">
                  <b-button
                    variant="primary"
                    class="mr-2 mb-2"
                    :disabled="product.stock === 0"
                    @click="addItemToCart"
                  >
                    Add to Cart
                  </b-button>
                  <b-button
                    variant="success"
                    class="mb-2"
                    :disabled="product.stock === 0"
                    @click="buyNow"
                  >
                    Buy Now
                  </b-button>
                </div>
              </div>
            </div>
          </b-col>
        </b-row>

        <!-- Related Products -->
        <div class="related-products mt-5">
          <h3 class="section-title">Related Products</h3>
          <b-row>
            <b-col v-for="relatedProduct in relatedProducts" :key="relatedProduct.id" cols="6" md="3" class="mb-4">
              <div class="card product-card">
                <router-link :to="`/product/${relatedProduct.id}`">
                  <b-img :src="relatedProduct.image" :alt="relatedProduct.name" fluid class="card-img-top"></b-img>
                </router-link>
                <div class="card-body">
                  <router-link :to="`/product/${relatedProduct.id}`" class="product-title">{{ relatedProduct.name }}</router-link>
                  <div class="product-price">Rs. {{ relatedProduct.price.toLocaleString() }}</div>
                  <div class="product-actions mt-3">
                    <b-button variant="primary" size="sm" @click="addToCart(relatedProduct)" block>Add to Cart</b-button>
                  </div>
                </div>
              </div>
            </b-col>
          </b-row>
        </div>
      </div>
    </b-container>
  </div>
</template>

<script>
import { mapState, mapActions } from 'vuex'

export default {
  name: 'ProductDetails',
  data() {
    return {
      productId: null,
      quantity: 1,
      loading: false
    }
  },
  computed: {
    ...mapState(['products']),
    product() {
      return this.products.find(p => p.id === parseInt(this.productId))
    },
    relatedProducts() {
      if (!this.product) return []

      return this.products
        .filter(p => p.category === this.product.category && p.id !== this.product.id)
        .slice(0, 4)
    }
  },
  watch: {
    '$route.params.id'() {
      this.updateProductId()
      // Reset quantity when product changes
      this.quantity = 1
    }
  },
  methods: {
    ...mapActions(['addToCart', 'fetchProducts']),
    updateProductId() {
      this.productId = parseInt(this.$route.params.id)
    },
    addItemToCart() {
      if (this.product && this.quantity > 0) {
        // Add item with specified quantity
        for (let i = 0; i < this.quantity; i++) {
          this.addToCart(this.product)
        }
        this.$bvToast.toast(`${this.quantity} ${this.product.name} added to cart`, {
          title: 'Added to Cart',
          variant: 'success',
          toaster: 'b-toaster-bottom-right',
          autoHideDelay: 3000
        })
      }
    },
    buyNow() {
      this.addItemToCart()
      this.$router.push('/cart')
    }
  },
  created() {
    this.updateProductId()
    if (this.products.length === 0) {
      this.loading = true
      this.fetchProducts().finally(() => {
        this.loading = false
      })
    }
  }
}
</script>

<style lang="scss" scoped>
.page-header {
  background-color: #f8f9fa;

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

.product-image-container {
  background: white;
  border-radius: 8px;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
  padding: 2rem;
  display: flex;
  align-items: center;
  justify-content: center;
  height: 400px;

  .product-image {
    max-height: 100%;
    object-fit: contain;
  }
}

.product-info {
  .product-title {
    font-size: 1.8rem;
    font-weight: 600;
    margin-bottom: 0.5rem;
  }

  .product-price {
    font-size: 1.5rem;
    font-weight: 700;
    color: #3498db;
  }

  .product-description {
    h5 {
      font-size: 1.1rem;
      font-weight: 600;
      margin-bottom: 0.5rem;
    }

    p {
      color: #666;
      line-height: 1.6;
    }
  }

  .stock-status {
    font-weight: 500;

    &.in-stock {
      color: #27ae60;
    }

    &.out-of-stock {
      color: #e74c3c;
    }
  }

  .stock-quantity {
    color: #e67e22;
  }
}

.related-products {
  .section-title {
    position: relative;
    margin-bottom: 1.5rem;
    padding-bottom: 0.5rem;

    &:after {
      content: '';
      position: absolute;
      bottom: 0;
      left: 0;
      width: 60px;
      height: 2px;
      background-color: #3498db;
    }
  }
}
</style>
