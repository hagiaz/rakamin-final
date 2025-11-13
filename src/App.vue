<style src="/src/assets/style/page.css"></style>

<template>
  <div :class="['page-root', pageClass]">
    <div class="page-band" aria-hidden="true">
      <div class="pattern" />
    </div>

    <div class="card-wrap">
      <ProductDisplay
        :product="product"
        :loading="loading"
        @buy-now="buyNow"
        @next-product="nextProduct"
      />
    </div>
  </div>
</template>

<script>
import ProductDisplay from './components/productDisplay.vue'

export default {
  name: 'App',
  components: { ProductDisplay },
  data() {
    return {
      index: 1,
      product: null,
      loading: false,
    }
  },
  computed: {
    pageClass() {
      if (!this.product) return 'page-unavailable'
      if (this.product.category === "men's clothing") return 'page-men'
      if (this.product.category === "women's clothing") return 'page-women'
      return 'page-unavailable'
    },
  },
  methods: {
    formatPrice(v) {
      return Number(v).toFixed(2)
    },
    formatRate(r) {
      const num = Number(r || 0)
      return (Math.round(num * 10) / 10).toFixed(1)
    },
    async fetchProduct() {
      this.loading = true
      try {
        const res = await fetch(`https://fakestoreapi.com/products/${this.index}`)
        const data = await res.json()

        if (data && (data.category === "men's clothing" || data.category === "women's clothing")) {
          data.rating = data.rating || { rate: 0, count: 0 }
          data.rating.rate = Number(data.rating.rate || 0)
          this.product = data
        } else {
          this.product = null
        }
      } catch (err) {
        console.error('fetch error:', err)
        this.product = null
      } finally {
        setTimeout(() => (this.loading = false), 220)
      }
    },
    nextProduct() {
      this.index = this.index === 20 ? 1 : this.index + 1
      this.fetchProduct()
    },
    buyNow() {
      window.alert('Buy flow not implemented in demo.')
    },
  },
  mounted() {
    this.fetchProduct()
  },
}
</script>
