<template>
  <div class="products-page">
    <div class="page-header bg-light-gray py-4">
      <b-container>
        <h1 class="page-title">All Products</h1>
        <nav aria-label="breadcrumb">
          <ol class="breadcrumb">
            <li class="breadcrumb-item">
              <router-link to="/">Home</router-link>
            </li>
            <li class="breadcrumb-item active" aria-current="page">
              Products
            </li>
          </ol>
        </nav>
      </b-container>
    </div>

    <b-container class="py-4">
      <b-row>
        <!-- Filters Sidebar -->
        <b-col md="3" class="mb-4 mb-md-0">
          <div class="filters-sidebar">
            <div class="filter-section">
              <h4 class="filter-header">Categories</h4>
              <b-form-group>
                <b-form-checkbox-group
                  v-model="selectedCategories"
                  stacked
                >
                  <b-form-checkbox
                    v-for="category in categories"
                    :key="category"
                    :value="category"
                  >
                    {{ category }}
                  </b-form-checkbox>
                </b-form-checkbox-group>
              </b-form-group>
            </div>

            <div class="filter-section">
              <h4 class="filter-header">Price Range</h4>
              <b-form-group>
                <b-row>
                  <b-col cols="6">
                    <b-form-input
                      v-model="priceRange.min"
                      type="number"
                      min="0"
                      placeholder="Min"
                    ></b-form-input>
                  </b-col>
                  <b-col cols="6">
                    <b-form-input
                      v-model="priceRange.max"
                      type="number"
                      min="0"
                      placeholder="Max"
                    ></b-form-input>
                  </b-col>
                </b-row>
              </b-form-group>
            </div>

            <b-button
              variant="primary"
              block
              class="mt-3"
              @click="applyFilters"
            >
              Apply Filters
            </b-button>

            <b-button
              variant="outline-secondary"
              block
              class="mt-2"
              @click="resetFilters"
            >
              Reset Filters
            </b-button>
          </div>
        </b-col>

        <!-- Products Grid -->
        <b-col md="9">
          <div class="products-controls mb-4">
            <b-row align-v="center">
              <b-col sm="6" class="mb-2 mb-sm-0">
                <b-form-input
                  v-model="searchQuery"
                  placeholder="Search products..."
                  @input="applyFilters"
                ></b-form-input>
              </b-col>
              <b-col sm="6">
                <div class="d-flex justify-content-sm-end">
                  <b-form-select
                    v-model="sortBy"
                    :options="sortOptions"
                    class="w-auto"
                    @change="applyFilters"
                  ></b-form-select>
                </div>
              </b-col>
            </b-row>
          </div>

          <b-alert :show="filteredProducts.length === 0" variant="info">
            No products found matching your criteria. Try adjusting your filters.
          </b-alert>

          <b-row v-if="filteredProducts.length > 0">
            <b-col v-for="product in filteredProducts" :key="product.id" cols="6" md="4" lg="4" class="mb-4">
              <div class="card product-card">
                <router-link :to="`/product/${product.id}`">
                  <b-img :src="product.image" :alt="product.name" fluid class="card-img-top"></b-img>
                </router-link>
                <div class="card-body">
                  <router-link :to="`/product/${product.id}`" class="product-title">{{ product.name }}</router-link>
                  <div class="product-price">Rs. {{ product.price.toLocaleString() }}</div>
                  <p class="product-category small text-muted">{{ product.category }}</p>
                  <div class="product-actions">
                    <b-button variant="primary" @click="addToCart(product)" block>Add to Cart</b-button>
                  </div>
                </div>
              </div>
            </b-col>
          </b-row>
        </b-col>
      </b-row>
    </b-container>
  </div>
</template>

<script>
import { mapState, mapActions } from 'vuex'

export default {
  name: 'Products',
  data() {
    return {
      selectedCategories: [],
      priceRange: {
        min: '',
        max: ''
      },
      searchQuery: '',
      sortBy: 'price-low',
      currentPage: 1,
      itemsPerPage: 12,
      sortOptions: [
        { value: 'price-low', text: 'Price (Low to High)' },
        { value: 'price-high', text: 'Price (High to Low)' },
        { value: 'name-asc', text: 'Name (A to Z)' },
        { value: 'name-desc', text: 'Name (Z to A)' }
      ]
    }
  },
  computed: {
    ...mapState(['products']),
    categories() {
      // Get unique categories from products
      const uniqueCategories = [...new Set(this.products.map(product => product.category))]
      return uniqueCategories.sort()
    },
    filteredProducts() {
      let result = [...this.products]

      // Apply category filter
      if (this.selectedCategories.length > 0) {
        result = result.filter(product =>
          this.selectedCategories.includes(product.category)
        )
      }

      // Apply price filter
      if (this.priceRange.min) {
        result = result.filter(product =>
          product.price >= Number(this.priceRange.min)
        )
      }
      if (this.priceRange.max) {
        result = result.filter(product =>
          product.price <= Number(this.priceRange.max)
        )
      }

      // Apply search query
      if (this.searchQuery) {
        const query = this.searchQuery.toLowerCase()
        result = result.filter(product =>
          product.name.toLowerCase().includes(query) ||
          product.description.toLowerCase().includes(query) ||
          product.category.toLowerCase().includes(query)
        )
      }

      // Apply sorting
      switch(this.sortBy) {
        case 'price-low':
          result.sort((a, b) => a.price - b.price)
          break
        case 'price-high':
          result.sort((a, b) => b.price - a.price)
          break
        case 'name-asc':
          result.sort((a, b) => a.name.localeCompare(b.name))
          break
        case 'name-desc':
          result.sort((a, b) => b.name.localeCompare(a.name))
          break
      }

      return result
    },
    paginatedProducts() {
      const startIndex = (this.currentPage - 1) * this.itemsPerPage
      const endIndex = startIndex + this.itemsPerPage
      return this.filteredProducts.slice(startIndex, endIndex)
    }
  },
  methods: {
    ...mapActions(['addToCart', 'fetchProducts']),
    applyFilters() {
      // Reset to first page when filters change
      this.currentPage = 1
    },
    resetFilters() {
      this.selectedCategories = []
      this.priceRange.min = ''
      this.priceRange.max = ''
      this.searchQuery = ''
      this.sortBy = 'price-low'
      this.currentPage = 1
    }
  },
  created() {
    // Apply category filter from URL query if exists
    const categoryParam = this.$route.query.category
    if (categoryParam) {
      this.selectedCategories = [categoryParam]
    }

    // Load products if not already loaded
    if (this.products.length === 0) {
      this.fetchProducts()
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

.filters-sidebar {
  background: white;
  border-radius: 8px;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
  padding: 1.5rem;

  .filter-section {
    margin-bottom: 1.5rem;

    &:last-child {
      margin-bottom: 0;
    }
  }

  .filter-header {
    font-size: 1.1rem;
    font-weight: 600;
    margin-bottom: 1rem;
    padding-bottom: 0.5rem;
    border-bottom: 1px solid #eee;
  }
}

.products-controls {
  background: white;
  border-radius: 8px;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
  padding: 1rem;
}

// 产品卡片样式
.product-card {
  height: 100%;
  display: flex;
  flex-direction: column;
  transition: transform 0.3s, box-shadow 0.3s;
  border: 1px solid #eee;
  overflow: hidden;
  box-shadow: 0 2px 10px rgba(0, 0, 0, 0.05);
  border-radius: 8px;
  
  &:hover {
    transform: translateY(-5px);
    box-shadow: 0 5px 20px rgba(0, 0, 0, 0.1);
  }
  
  .card-img-top {
    height: 200px; // 固定图片高度
    object-fit: contain; // 保持图片比例
    padding: 1rem;
    background-color: #f9f9f9;
  }
  
  .card-body {
    padding: 1.25rem;
    display: flex;
    flex-direction: column;
    flex-grow: 1;
  }
  
  .product-title {
    font-size: 1rem;
    font-weight: 600;
    color: #333;
    text-decoration: none;
    margin-bottom: 0.5rem;
    display: block;
    overflow: hidden;
    text-overflow: ellipsis;
    display: -webkit-box;
    -webkit-line-clamp: 2;
    line-clamp: 2;
    -webkit-box-orient: vertical;
    height: 3em;
    line-height: 1.5;
    
    &:hover {
      color: #3498db;
    }
  }
  
  .product-price {
    font-size: 1.25rem;
    font-weight: 700;
    color: #e74c3c;
    margin-bottom: 0.75rem;
  }
  
  .product-category {
    margin-bottom: 1rem;
    flex-grow: 1;
  }
  
  .product-actions {
    margin-top: auto;
    
    .btn-primary {
      border-radius: 4px;
      padding: 0.5rem;
      font-weight: 500;
      transition: all 0.2s;
      
      &:hover {
        transform: translateY(-2px);
      }
    }
  }
}

// 对不同屏幕尺寸调整卡片尺寸
@media (max-width: 767.98px) {
  .product-card {
    .card-img-top {
      height: 180px;
    }
    
    .product-title {
      font-size: 0.9rem;
    }
    
    .product-price {
      font-size: 1.1rem;
    }
  }
}
</style>
