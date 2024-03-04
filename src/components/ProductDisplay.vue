<template>
  <div class="container">
    <div id="loader" v-if="loading"></div>
    <div class="background" v-if="category !== ''"></div>
    <div :class="{'men-section': category === 'men\'s clothing', 'women-section': category === 'women\'s clothing', 'unavailable-product': category !== 'men\'s clothing' && category !== 'women\'s clothing'}">
      <div class="product-details" v-if="!productUnavailable">
        <img :src="product.image" class="product-image" alt="Product Image">
        <div class="product-info">
          <h1>{{ product.title }}</h1>
          <div class="info-container">
            <div class="category">{{ category }}</div>
            <div class="rating">
              <span>{{ product.rating.rate }}/5</span>
              <div class="ellipse-rating">
                <svg class="ellipse" width="18" height="18">
                  <circle cx="10" cy="10" r="7" :fill="ratingColor" />
                </svg>
                <svg class="ellipse" width="18" height="18">
                  <circle cx="10" cy="10" r="7" :fill="ratingColor" />
                </svg>
                <svg class="ellipse" width="18" height="18">
                  <circle cx="10" cy="10" r="7" :fill="ratingColor" />
                </svg>
                <svg class="ellipse" width="18" height="18">
                  <circle cx="10" cy="10" r="7" :fill="ratingColor" />
                </svg>
                <svg class="ellipse" width="18" height="18">
                  <circle cx="10" cy="10" r="7" :fill="ratingColor" />
                </svg>
              </div>
            </div>
          </div>
          <hr class="custom-hr">
          <p class="description">{{ product.description }}</p>
          <hr class="custom-hr">
          <div class="price">${{ product.price }}</div>
          <div class="button-container">
            <button class="buy-now" @click="buyNow()">Buy Now</button>
            <button class="next-product" @click="fetchNextProduct()">Next Product</button>
          </div>
        </div>
      </div>
     <div class="container-un" v-else>
        <!-- Tampilkan halaman produk tidak tersedia -->
        <div class="background-un" v-if="category === ''"></div>
        <div class="unavailable-product">
            <div class="tengah">
              <p>This product is unavailable to show</p>
              <button class="next-product-un" @click="fetchNextProduct">Next Product</button>
            </div>
        </div>
      </div>
      <!-- Tampilkan halaman produk tidak tersedia -->
    </div>
  </div>
</template>


<script>
export default {
  data() {
    return {
      product: {},
      category: '',
      loading: false, 
      productUnavailable: false, 
      backgroundStyle: '',
    };
  },
  created() {
    this.fetchProduct();
  },
  computed: {
    ratingColor() {
      return this.product.rating.rate >= 1 ? 'var(--primary-color)' : '#ffffff';
    },
  },
  methods: {
    async fetchProduct() {
       this.loading = true; 
      let category = ''; 
      const randomCategoryIndex = Math.floor(Math.random() * 2); 
      if (randomCategoryIndex === 0) {
        category = "men's clothing";
        document.documentElement.style.setProperty('--primary-color', '#002772'); 
      } else {
        category = "women's clothing";
        document.documentElement.style.setProperty('--primary-color', '#720060');
      }

      const response = await fetch(`https://fakestoreapi.com/products/category/${category}`);
      const data = await response.json();
      const randomProductIndex = Math.floor(Math.random() * data.length); 
      this.product = data[randomProductIndex];
      this.category = category;
      this.setRatingColor();
       this.loading = false
    },
    async fetchNextProduct() {
      this.loading = true; 
      await this.fetchProduct();
      this.loading = false; 
      this.productUnavailable = false; 
    },
    setRatingColor() {
      this.$nextTick(() => {
        const ratingCircles = document.querySelectorAll('.ellipse-rating circle');
        ratingCircles.forEach((circle, index) => {
          if (index < Math.round(this.product.rating.rate)) {
            circle.setAttribute('fill', 'var(--primary-color)'); 
          } else {
            circle.setAttribute('fill', '#ffffff'); 
          }
        });
      });
    },
     buyNow() {
      this.productUnavailable = true; 
      this.backgroundStyle = 'var(--product-unavailable)'; 
    },
  },
};
</script>


<style scoped>
@import url('../assets/Style/style.css');
</style>
