<template>
  <div class="home">
    <!-- Hero Section -->
    <section class="hero">
      <b-container>
        <b-row class="align-items-center">
          <b-col md="6" class="mb-4 mb-md-0">
            <div class="hero-content">
              <h1 class="hero-title">Shop Daily Necessities</h1>
              <h2 class="hero-subtitle">Quality Products at Affordable Prices</h2>
              <p class="hero-text">
                Find all your daily household needs in one place. From groceries to electronics, we have everything you need for your home.
              </p>
              <b-button to="/products" variant="primary" size="lg" class="mt-3">Shop Now</b-button>
            </div>
          </b-col>
          <b-col md="6">
            <b-img src="https://m.media-amazon.com/images/I/51+EVN+riAL._AC_UL640_FMwebp_QL65_.jpg" alt="Daily Necessities" fluid rounded></b-img>
          </b-col>
        </b-row>
      </b-container>
    </section>

    <!-- Categories Section -->
    <section class="categories py-5 bg-light-gray">
      <b-container>
        <h2 class="section-title">Shop by Category</h2>
        <b-row>
          <b-col v-for="category in categories" :key="category.id" cols="6" md="4" lg="3" class="mb-4">
            <router-link :to="`/products?category=${category.name}`" class="category-card">
              <div class="card product-card h-100">
                <div class="category-img">
                  <b-img :src="category.image" :alt="category.name" fluid></b-img>
                </div>
                <div class="card-body text-center">
                  <h5 class="category-name">{{ category.name }}</h5>
                </div>
              </div>
            </router-link>
          </b-col>
        </b-row>
      </b-container>
    </section>

    <!-- Featured Products -->
    <section class="featured-products py-5">
      <b-container>
        <h2 class="section-title">Featured Products</h2>
        <b-row>
          <b-col v-for="product in featuredProducts" :key="product.id" cols="6" md="4" lg="3" class="mb-4">
            <div class="card product-card h-100">
              <router-link :to="`/product/${product.id}`">
                <b-img :src="product.image" :alt="product.name" fluid class="card-img-top"></b-img>
              </router-link>
              <div class="card-body d-flex flex-column">
                <router-link :to="`/product/${product.id}`" class="product-title">{{ product.name }}</router-link>
                <div class="product-price">Rs. {{ product.price.toLocaleString() }}</div>
                <p class="product-category small text-muted flex-grow-1">{{ product.category }}</p>
                <div class="product-actions mt-auto">
                  <b-button variant="primary" @click="addToCart(product)" block>Add to Cart</b-button>
                </div>
              </div>
            </div>
          </b-col>
        </b-row>
        <div class="text-center mt-4">
          <b-button to="/products" variant="outline-primary" size="lg">View All Products</b-button>
        </div>
      </b-container>
    </section>

    <!-- Advantages Section -->
    <section class="advantages py-5 bg-light-gray">
      <b-container>
        <h2 class="section-title">Why Choose Us</h2>
        <b-row>
          <b-col md="4" class="mb-4 mb-md-0">
            <div class="advantage-card text-center">
              <div class="advantage-icon">
                <b-icon icon="truck" font-scale="3"></b-icon>
              </div>
              <h4 class="mt-3">Free Delivery</h4>
              <p>Free delivery on orders above Rs. 2000</p>
            </div>
          </b-col>
          <b-col md="4" class="mb-4 mb-md-0">
            <div class="advantage-card text-center">
              <div class="advantage-icon">
                <b-icon icon="shield-check" font-scale="3"></b-icon>
              </div>
              <h4 class="mt-3">Quality Products</h4>
              <p>All products are quality checked</p>
            </div>
          </b-col>
          <b-col md="4">
            <div class="advantage-card text-center">
              <div class="advantage-icon">
                <b-icon icon="headset" font-scale="3"></b-icon>
              </div>
              <h4 class="mt-3">24/7 Support</h4>
              <p>Customer support available all day</p>
            </div>
          </b-col>
        </b-row>
      </b-container>
    </section>
  </div>
</template>

<script>
import { mapState, mapActions } from 'vuex'

export default {
  name: 'Home',
  data() {
    return {
      categories: [
        { id: 1, name: 'Groceries', image: 'https://m.media-amazon.com/images/I/91d8vAw+TcL._AC_UL640_FMwebp_QL65_.jpg' },
        { id: 2, name: 'Cleaning', image: 'https://m.media-amazon.com/images/I/71nvEUoknbL._AC_UL640_FMwebp_QL65_.jpg' },
        { id: 3, name: 'Electronics', image: 'https://m.media-amazon.com/images/I/516tnauVb+L._AC_UY436_FMwebp_QL65_.jpg' },
        { id: 4, name: 'Health & Hygiene', image: 'https://m.media-amazon.com/images/I/619HiVU9ASL._AC_UL640_FMwebp_QL65_.jpg' },
        { id: 5, name: 'Kitchen Appliances', image: 'https://m.media-amazon.com/images/I/81O-rWbThKL._AC_UL640_FMwebp_QL65_.jpg' },
        { id: 6, name: 'Home & Living', image: 'https://m.media-amazon.com/images/I/71uVKmY3i8L._AC_UL640_FMwebp_QL65_.jpg' },
        { id: 7, name: 'Beverages', image: 'https://m.media-amazon.com/images/I/61E2EX8j7OL._AC_UL640_FMwebp_QL65_.jpg' },
        { id: 8, name: 'Accessories', image: 'https://m.media-amazon.com/images/I/81idlqFqcUL._AC_UY436_FMwebp_QL65_.jpg' }
      ]
    }
  },
  computed: {
    ...mapState(['products']),
    featuredProducts() {
      return this.products.slice(0, 8)
    }
  },
  methods: {
    ...mapActions(['addToCart', 'fetchProducts']),
  },
  created() {
    if (this.products.length === 0) {
      this.fetchProducts()
    }
  }
}
</script>

<style lang="scss" scoped>
.hero {
  padding: 4rem 0;
  background: linear-gradient(to right, #e6f7ff, #ffffff);

  &-title {
    font-size: 2.5rem;
    font-weight: 700;
    margin-bottom: 1rem;
    color: #333;
  }

  &-subtitle {
    font-size: 1.5rem;
    font-weight: 500;
    margin-bottom: 1rem;
    color: #3498db;
  }

  &-text {
    font-size: 1.1rem;
    color: #666;
    margin-bottom: 1.5rem;
  }
}

// 特色产品卡片图片样式
.featured-products {
  .card-img-top {
    height: 200px; // 固定图片高度
    object-fit: contain; // 保持图片比例
    padding: 1rem;
    background-color: #f9f9f9;
  }
}

.category-card {
  display: block;
  text-decoration: none;

  .category-img {
    height: 180px;
    display: flex;
    align-items: center;
    justify-content: center;
    padding: 1rem;
    background-color: #f9f9f9;

    img {
      max-height: 100%;
      object-fit: contain;
    }
  }

  .category-name {
    font-size: 1.1rem;
    font-weight: 600;
    color: #333;
    margin: 0;
  }
}

.advantage-card {
  padding: 2rem;
  background: white;
  border-radius: 10px;
  box-shadow: 0 3px 10px rgba(0, 0, 0, 0.05);
  height: 100%;

  .advantage-icon {
    color: #3498db;
    margin-bottom: 1rem;
  }

  h4 {
    font-weight: 600;
    font-size: 1.2rem;
    margin-bottom: 0.5rem;
  }

  p {
    color: #666;
    margin-bottom: 0;
  }
}
</style>
