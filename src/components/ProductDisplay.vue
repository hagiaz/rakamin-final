<template>
  <main class="card" role="region" aria-label="Product card">
    <template v-if="loading">
      <section class="product">
        <div class="left">
          <div class="skeleton skeleton-image" />
        </div>

        <div class="right">
          <div class="skeleton skeleton-title" />
          <div class="skeleton skeleton-category" />
          <div class="skeleton skeleton-rating" />
          <div class="divider" />
          <div v-for="n in 4" :key="n" class="skeleton skeleton-desc" />
          <div class="bottom-row">
            <div class="left-bottom">
              <div class="skeleton skeleton-price" />
            </div>
            <div class="right-bottom">
              <div class="skeleton skeleton-button" />
              <div class="skeleton skeleton-button small" />
            </div>
          </div>
        </div>
      </section>
    </template>

    <template v-else-if="product">
      <section class="product">
        <div class="left">
          <img :src="product.image" :alt="product.title" class="product-image" />
        </div>

        <div class="right">
          <h1 class="title">{{ product.title }}</h1>

          <div class="meta-row">
            <div class="meta-left">
              <div class="category">{{ product.category }}</div>
            </div>

            <div class="meta-right">
              <div class="rating" aria-hidden="true">
                <span class="rate">{{ formatRate(product.rating.rate) }}/5</span>
                <div class="dots">
                  <span
                    v-for="n in 5"
                    :key="n"
                    :class="{ filled: n <= Math.round(product.rating.rate) }"
                  />
                </div>
              </div>
            </div>
          </div>

          <div class="divider" />

          <p class="description">{{ product.description }}</p>

          <div class="bottom-row">
            <div class="left-bottom">
              <div class="price">${{ formatPrice(product.price) }}</div>
            </div>

            <div class="right-bottom">
              <button class="btn buy" @click="$emit('buy-now')">Buy now</button>
              <button class="btn next" @click="$emit('next-product')">Next product</button>
            </div>
          </div>
        </div>
      </section>
    </template>

    <template v-else>
      <section class="unavailable">
        <svg class="sad-illus" viewBox="0 0 200 120" aria-hidden="true">
          <g fill="none" stroke="#bfc4c8" stroke-width="5" stroke-linecap="round">
            <path d="M30 40c12-18 36-18 48 0" />
            <path d="M122 40c12-18 36-18 48 0" />
            <path d="M40 90c30 20 70 20 100 0" />
          </g>
        </svg>

        <p class="unavail-text">This product is unavailable to show</p>

        <div style="margin-top: 18px">
          <button class="btn next" @click="$emit('next-product')">Next product</button>
        </div>
      </section>
    </template>
  </main>
</template>

<script>
export default {
  name: 'ProductDisplay',
  props: {
    product: Object,
    loading: Boolean,
  },
  methods: {
    formatPrice(v) {
      return Number(v).toFixed(2)
    },
    formatRate(r) {
      const num = Number(r || 0)
      return (Math.round(num * 10) / 10).toFixed(1)
    },
  },
}
</script>
