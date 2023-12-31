<template>
  <div id="app">
    <ProductDisplay
      title="Stylish Dress"
      type="Dress"
      rating="4"
      description="A stylish and trendy dress for any occasion."
      price="$49.99"
    />
  </div>
</template>

<script>
import ProductDisplay from './components/ProductDisplay.vue'

export default {
  name: 'App',
  components: {
    ProductDisplay,
  }
}
</script>

<style>
</style>
