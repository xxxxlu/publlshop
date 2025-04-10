<template>
  <footer class="site-footer py-4 mt-auto">
    <b-container>
      <div class="row">
        <div class="col-md-4 mb-4 mb-md-0">
          <h5 class="footer-title">
            <span class="text-primary">Publ</span>lshop
          </h5>
          <p class="text-muted">
            Your one-stop shop for all daily necessities in Pakistan.
            Best quality products at affordable prices.
          </p>
        </div>

        <div class="col-md-2 mb-4 mb-md-0">
          <h5 class="footer-heading">Links</h5>
          <ul class="list-unstyled">
            <li><router-link to="/">Home</router-link></li>
            <li><router-link to="/products">Products</router-link></li>
            <li><router-link to="/contact">Contact</router-link></li>
          </ul>
        </div>

        <div class="col-md-3 mb-4 mb-md-0">
          <h5 class="footer-heading">Contact</h5>
          <ul class="list-unstyled">
            <li>
              <b-icon icon="geo-alt" variant="primary"></b-icon>
              123 Main Street, Karachi
            </li>
            <li>
              <b-icon icon="telephone" variant="primary"></b-icon>
              +92 123 456 7890
            </li>
            <li>
              <b-icon icon="envelope" variant="primary"></b-icon>
              info@publlshop.com
            </li>
          </ul>
        </div>

        <div class="col-md-3">
          <h5 class="footer-heading">Payment Methods</h5>
          <div class="payment-methods">
            <div class="payment-icon jazzcash">
              <img src="../../assets/jazzcash.png" alt="JazzCash" />
            </div>
            <div class="payment-icon easypaisa">
              <img src="../../assets/easypaisa.png" alt="EasyPaisa" />
            </div>
            <div>Cash on Delivery</div>
          </div>
        </div>
      </div>

      <hr class="my-4" />

      <div class="row">
        <div class="col-md-6 text-center text-md-left">
          <p class="mb-0">
            &copy; {{ new Date().getFullYear() }} Publlshop. All rights reserved.
          </p>
        </div>
        <div class="col-md-6 text-center text-md-right">
          <div class="social-icons">
            <a href="#" class="social-icon">
              <b-icon icon="facebook" font-scale="1.2"></b-icon>
            </a>
            <a href="#" class="social-icon">
              <b-icon icon="instagram" font-scale="1.2"></b-icon>
            </a>
            <a href="#" class="social-icon">
              <b-icon icon="twitter" font-scale="1.2"></b-icon>
            </a>
          </div>
        </div>
      </div>
    </b-container>
  </footer>
</template>

<script>
export default {
  name: 'TheFooter'
}
</script>

<style lang="scss" scoped>
.site-footer {
  background-color: #f8f9fa;
  border-top: 1px solid #e9ecef;
}

.footer-title {
  font-size: 1.4rem;
  font-weight: bold;
  margin-bottom: 1rem;

  .text-primary {
    color: #3498db !important;
  }
}

.footer-heading {
  font-size: 1.1rem;
  font-weight: 600;
  margin-bottom: 1.2rem;
  position: relative;

  &:after {
    content: '';
    display: block;
    width: 40px;
    height: 2px;
    background: #3498db;
    margin-top: 0.5rem;
  }
}

a {
  color: #6c757d;
  text-decoration: none;
  transition: color 0.2s;

  &:hover {
    color: #3498db;
    text-decoration: none;
  }
}

.payment-methods {
  display: flex;
  flex-wrap: wrap;
  gap: 10px;
  align-items: center;
}

.payment-icon {
  width: 60px;
  height: 40px;
  background: white;
  border-radius: 4px;
  display: flex;
  align-items: center;
  justify-content: center;
  padding: 5px;
  box-shadow: 0 2px 4px rgba(0,0,0,0.1);

  img {
    max-width: 100%;
    max-height: 100%;
    object-fit: contain;
  }
}

.social-icons {
  display: flex;
  gap: 1rem;
  justify-content: flex-end;

  .social-icon {
    width: 36px;
    height: 36px;
    background: #e9ecef;
    border-radius: 50%;
    display: flex;
    align-items: center;
    justify-content: center;
    transition: all 0.2s;

    &:hover {
      background: #3498db;
      color: white;
    }
  }
}

@media (max-width: 767.98px) {
  .social-icons {
    justify-content: center;
    margin-top: 1rem;
  }
}
</style>
