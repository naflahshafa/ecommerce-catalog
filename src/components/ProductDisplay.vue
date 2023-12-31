<template>
  <div class="product-display" :class="{'page-men': isMenCategory, 'page-women': isWomenCategory, 'page-unavailable': isUnavailableCategory}">
    <div v-if="loading">Loading...</div>

    <div v-if="error">Error fetching data: {{ error }}</div>

    <div v-if="!isUnavailableCategory">
      <div v-if="isProductAvailable && !loading">
        <div class="product-card">
          <div class="product-image">
            <img :src="product.image" alt="Product Image"/>
          </div>
          <div class="group">
            <h2 :style="{ color: categoryColor, 'border-bottom-color': categoryColor }">{{ product.title }}</h2>
            <h4>{{ product.category }}</h4>
            <p>Rating: {{ product.rating.rate }} / 5 ({{ product.rating.count }} reviews)</p>
            <p>{{ product.description }}</p>
            <h2 :style="{ color: categoryColor }">${{ product.price }}</h2>
            <div class="-product-button">
              <button :style="{ background: categoryColor }" @click="buyNow">Buy Now</button>
              <button :style="{ background: categoryColor }" @click="nextProduct">Next Product</button>
            </div>
          </div>
        </div>
      </div>
      <div v-else>
        <p>Product unavailable.</p>
        <button @click="nextProduct">Next Product</button>
      </div>
    </div>
    <div v-else>
      <p>Product unavailable.</p>
      <button @click="nextProduct">Next Product</button>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      loading: true,
      error: null,
      product: null,
      categoryColor: '',
      productIndex: 1, // Index awal
      isProductAvailable: true,
    };
  },
  mounted() {
    // Panggil API di sini
    this.fetchDataFromApi();
  },
  methods: {
    async fetchDataFromApi() {
      try {
        const response = await fetch('https://fakestoreapi.com/products');
        const data = await response.json();

        if (Array.isArray(data) && data.length > 0) {
          this.product = data[0];
          this.setCategoryColor(this.product.category);
        } else {
          throw new Error('Invalid API response');
        }
      } catch (error) {
        this.error = error.message;
      } finally {
        this.loading = false;
      }
    },
    setCategoryColor(category) {
      if (category === "men's clothing") {
        this.categoryColor = '#002772';
      } else if (category === "women's clothing") {
        this.categoryColor = '#720006';
      } else {
        this.categoryColor = '#1E1E1E'; // Warna default atau atur sesuai kebutuhan
        this.isProductAvailable = false; // Set produk tidak tersedia jika kategori tidak sesuai
      }
    },
    buyNow() {
      alert('Buy Now clicked!');
      // Implementasikan fungsi Buy Now
    },
    async nextProduct() {
      try {
        const response = await fetch(`https://fakestoreapi.com/products/${this.productIndex}`);
        const product = await response.json();

        if (product) {
          this.product = product;
          this.setCategoryColor(this.product.category);
          this.isProductAvailable = true; // Set kembali produk tersedia
        } else {
          console.log('Invalid product data. Trying next product.');
          this.nextProduct();
          return;
        }

        this.productIndex++;

        if (this.productIndex > 20) {
          this.productIndex = 1;
        }
      } catch (error) {
        console.error('Error fetching data:', error);
      }
    },
  },
  computed: {
    isMenCategory() {
      return this.product && this.product.category === "men's clothing";
    },
    isWomenCategory() {
      return this.product && this.product.category === "women's clothing";
    },
    isUnavailableCategory() {
      return this.product && this.product.category !== "men's clothing" && this.product.category !== "women's clothing";
    },
  },
};
</script>

<style scoped>
@import '@/assets/style/page.css';
</style>
