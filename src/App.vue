<template>
  <div id="app">
    <ProductDisplay
      title=""
      category=""
      rating=""
      description=""
      price=""
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
