<template>
  <div class="product-display" :class="{'page-men': isMenCategory, 'page-women': isWomenCategory, 'page-unavailable': isUnavailableCategory}">
    <div v-if="loading" class="loader"></div>

    <div v-else-if="error">
      <div class="product-card product-img-unavailable-background">
        <div class="product-unavailable">
          <p>Error fetching data: {{ error }}</p>
          <button :style="{ background: categoryColor }" @click="nextProduct">Next Product</button>
        </div>
      </div>
    </div>

    <div v-else>
      <div v-if="isProductAvailable && (isMenCategory || isWomenCategory)">
        <div class="product-card">
          <div class="product-image">
            <img :src="product.image" alt="Product Image"/>
          </div>
          <div class="group">
            <h2 :style="{ color: categoryColor}">{{ product.title }}</h2>
            <div class="group2">
              <h4>{{ product.category }}</h4>
              <h4>Rating: {{ product.rating.rate }} / 5 ({{ product.rating.count }} reviews)</h4>
            </div>
            <p>{{ product.description }}</p>
            <h3 :style="{ color: categoryColor }">${{ product.price }}</h3>
            <div class="product-button">
              <button :style="{ background: categoryColor }" @click="buyNow">Buy Now</button>
              <button :style="{ background: categoryColor }" @click="nextProduct">Next Product</button>
            </div>
          </div>
        </div>
      </div>
      <div v-else-if="!isProductAvailable && isUnavailableCategory">
        <div class="product-card product-img-unavailable-background">
          <div class="product-unavailable">
            <p>This product is unavailable to show</p>
            <button :style="{ background: categoryColor }" @click="nextProduct">Next Product</button>
          </div>
        </div>
      </div>
      <div v-else>
        <div class="product-card product-img-unavailable-background">
          <div class="product-unavailable">
            <p>This product is unavailable to show</p>
            <button :style="{ background: categoryColor }" @click="nextProduct">Next Product</button>
          </div>
        </div>
      </div>
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
      productIndex: 1,
      isProductAvailable: true,
    };
  },
  mounted() {
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
        this.isProductAvailable = false;
        this.categoryColor = '#1E1E1E';
      }
    },
    buyNow() {
      alert('Buy Now clicked!');
    },
    async nextProduct() {
      try {
        this.loading = true;

        const response = await fetch(`https://fakestoreapi.com/products/${this.productIndex}`);
        const product = await response.json();

        if (product) {
          this.product = product;
          this.setCategoryColor(this.product.category);
          this.isProductAvailable = true;
        } else {
          console.log('Invalid product data. Trying next product.');
          this.nextProduct();
          return;
        }

        this.productIndex++;

        if (this.productIndex > 20) {
          this.productIndex = 1;
        }

        this.loading = false;
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
      return !this.isMenCategory && !this.isWomenCategory && !this.isProductAvailable;
    },
  },
};
</script>

<style scoped>
@import '@/assets/style/page.css';
</style>
