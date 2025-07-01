<template>
  <div class="contact-page">
    <div class="page-header bg-light-gray py-4">
      <b-container>
        <h1 class="page-title">Contact Us</h1>
        <nav aria-label="breadcrumb">
          <ol class="breadcrumb">
            <li class="breadcrumb-item">
              <router-link to="/">Home</router-link>
            </li>
            <li class="breadcrumb-item active" aria-current="page">Contact</li>
          </ol>
        </nav>
      </b-container>
    </div>

    <b-container class="py-5">
      <b-row>
        <!-- Contact Information -->
        <b-col lg="4" class="mb-4 mb-lg-0">
          <div class="contact-info">
            <h2 class="section-title">Get in Touch</h2>
            <p class="mb-4">
              Have questions about our products, services, or need assistance?
              We're here to help! Contact us using any of the methods below.
            </p>

            <div class="contact-method d-flex align-items-start mb-4">
              <div class="contact-icon mr-3">
                <b-icon icon="envelope" font-scale="1.5" variant="primary"></b-icon>
              </div>
              <div class="contact-details">
                <h5>Email Us</h5>
                <p class="text-muted mb-0">
                  publlshop@publlshop.fun<br>
                </p>
              </div>
            </div>

            <div class="contact-method d-flex align-items-start">
              <div class="contact-icon mr-3">
                <b-icon icon="clock" font-scale="1.5" variant="primary"></b-icon>
              </div>
              <div class="contact-details">
                <h5>Business Hours</h5>
                <p class="text-muted mb-0">
                  Monday - Friday: 9am - 6pm<br>
                  Saturday: 10am - 4pm<br>
                  Sunday: Closed
                </p>
              </div>
            </div>

            <div class="social-icons mt-4">
              <h5>Follow Us</h5>
              <div class="d-flex">
                <a href="#" class="social-icon">
                  <b-icon icon="facebook" font-scale="1.2"></b-icon>
                </a>
                <a href="#" class="social-icon">
                  <b-icon icon="instagram" font-scale="1.2"></b-icon>
                </a>
                <a href="#" class="social-icon">
                  <b-icon icon="twitter" font-scale="1.2"></b-icon>
                </a>
                <a href="#" class="social-icon">
                  <b-icon icon="whatsapp" font-scale="1.2"></b-icon>
                </a>
              </div>
            </div>
          </div>
        </b-col>

        <!-- Contact Form -->
        <b-col lg="8">
          <div class="contact-form-container">
            <h2 class="section-title">Send Us a Message</h2>
            <p class="mb-4">
              Fill out the form below and we'll get back to you as soon as possible.
            </p>

            <b-form @submit.prevent="submitForm">
              <b-row>
                <b-col md="6">
                  <b-form-group label="Your Name" label-for="name">
                    <b-form-input
                      id="name"
                      v-model="form.name"
                      placeholder="Enter your name"
                      required
                    ></b-form-input>
                  </b-form-group>
                </b-col>

                <b-col md="6">
                  <b-form-group label="Your Email" label-for="email">
                    <b-form-input
                      id="email"
                      v-model="form.email"
                      type="email"
                      placeholder="Enter your email"
                      required
                    ></b-form-input>
                  </b-form-group>
                </b-col>
              </b-row>

              <b-form-group label="Phone Number" label-for="phone">
                <b-form-input
                  id="phone"
                  v-model="form.phone"
                  placeholder="Enter your phone number"
                ></b-form-input>
              </b-form-group>

              <b-form-group label="Subject" label-for="subject">
                <b-form-input
                  id="subject"
                  v-model="form.subject"
                  placeholder="What is this regarding?"
                  required
                ></b-form-input>
              </b-form-group>

              <b-form-group label="Message" label-for="message">
                <b-form-textarea
                  id="message"
                  v-model="form.message"
                  placeholder="Enter your message here..."
                  rows="6"
                  required
                ></b-form-textarea>
              </b-form-group>

              <div class="text-right">
                <b-button type="submit" variant="primary" size="lg" :disabled="submitting">
                  <b-spinner small v-if="submitting" class="mr-1"></b-spinner>
                  {{ submitting ? 'Sending...' : 'Send Message' }}
                </b-button>
              </div>
            </b-form>
          </div>
        </b-col>
      </b-row>
    </b-container>

    <!-- Map Section -->
    <!-- <div class="map-section py-5 bg-light-gray">
      <b-container>
        <h2 class="section-title text-center mb-4">Find Us</h2>
        <div class="map-container">
          <iframe
            src="https://www.google.com/maps/embed?pb=!1m18!1m12!1m3!1d3401.5553921555834!2d74.35043937558968!3d31.51532674774296!2m3!1f0!2f0!3f0!3m2!1i1024!2i768!4f13.1!3m3!1m2!1s0x391905f7f66a7607%3A0xaef8bc4282c7b1!2sGulberg%20III%2C%20Lahore%2C%20Punjab%2C%20Pakistan!5e0!3m2!1sen!2s!4v1712354558493!5m2!1sen!2s"
            width="100%"
            height="400"
            style="border:0;"
            allowfullscreen=""
            loading="lazy"
            referrerpolicy="no-referrer-when-downgrade">
          </iframe>
        </div>
      </b-container>
    </div> -->
  </div>
</template>

<script>
export default {
  name: 'Contact',
  data() {
    return {
      form: {
        name: '',
        email: '',
        phone: '',
        subject: '',
        message: ''
      },
      submitting: false
    }
  },
  methods: {
    submitForm() {
      // In a real app, this would submit to an API endpoint
      // For this demo, we'll just simulate a submission

      this.submitting = true

      // Simulate API call
      setTimeout(() => {
        this.submitting = false

        // Show success message
        this.$bvToast.toast('Your message has been sent successfully. We\'ll get back to you soon!', {
          title: 'Message Sent',
          variant: 'success',
          toaster: 'b-toaster-bottom-right',
          autoHideDelay: 5000
        })

        // Reset form
        this.form = {
          name: '',
          email: '',
          phone: '',
          subject: '',
          message: ''
        }
      }, 1500)
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

.section-title {
  font-size: 1.8rem;
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

.contact-info, .contact-form-container {
  background: white;
  border-radius: 8px;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
  padding: 2rem;
  height: 100%;

  p {
    color: #666;
  }
}

.contact-method {
  .contact-icon {
    color: #3498db;
    width: 40px;
    display: flex;
    align-items: center;
    justify-content: center;
  }

  .contact-details {
    h5 {
      font-size: 1.1rem;
      font-weight: 600;
      margin-bottom: 0.5rem;
    }
  }
}

.social-icons {
  h5 {
    font-size: 1.1rem;
    font-weight: 600;
    margin-bottom: 1rem;
  }

  .social-icon {
    width: 40px;
    height: 40px;
    background: #f8f9fa;
    border-radius: 50%;
    display: flex;
    align-items: center;
    justify-content: center;
    color: #3498db;
    margin-right: 0.75rem;
    transition: all 0.2s;

    &:hover {
      background: #3498db;
      color: white;
    }
  }
}

.map-container {
  border-radius: 8px;
  overflow: hidden;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
}
</style>
