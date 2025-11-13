<template>
  <div :class="['page-root', pageClass]">
    <div class="page-band" aria-hidden="true">
      <div class="pattern" />
    </div>

    <div class="card-wrap">
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
                  <button class="btn buy" @click="buyNow">Buy now</button>
                  <button class="btn next" @click="nextProduct">Next product</button>
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
              <button class="btn next" @click="nextProduct">Next product</button>
            </div>
          </section>
        </template>
      </main>
    </div>
  </div>
</template>

<script>
export default {
  name: 'App',
  data() {
    return {
      index: 1, // 1..20
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

<style>
:root {
  --navy: #002772;
  --soft-pink: #fde2ff;
  --black: #1e1e1e;
  --plum: #720060;
  --light-blue: #d6e6ff;
  --dark-gray: #3f3f3f;
  --light-gray: #dcdcdc;
  --white: #ffffff;

  --card-radius: 10px;
  --card-shadow: rgba(2, 39, 114, 0.12);
  --divider: rgba(13, 36, 83, 0.06);
}

.page-root {
  min-height: 100vh;
  position: relative;
  font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, 'Helvetica Neue', Arial;
  color: var(--black);
  overflow-x: hidden;
  transition: background-color 300ms ease;
}

.page-men {
  background-color: var(--light-blue);
}

.page-women {
  background-color: var(--soft-pink);
}

.page-unavailable {
  background-color: var(--light-gray);
}

.page-band {
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  height: 44vh;
  z-index: 0;
}

.page-men .page-band {
  background: linear-gradient(180deg, var(--light-blue) 0%, var(--light-blue) 100%);
}
.page-women .page-band {
  background: linear-gradient(180deg, var(--soft-pink) 0%, var(--soft-pink) 100%);
}
.page-unavailable .page-band {
  background: linear-gradient(180deg, var(--light-gray) 0%, var(--light-gray) 100%);
}

.pattern {
  position: absolute;
  inset: 0;
  background-repeat: repeat;
  background-size: 220px 220px;
  transform: translateY(-8%);
  opacity: 0.85;
}

.card-wrap {
  position: relative;
  z-index: 10;
  display: flex;
  justify-content: center;
  align-items: center;
  padding-top: 8vh;
  padding-bottom: 72px;
}

.card {
  width: 820px;
  max-width: calc(100% - 40px);
  background: var(--white);
  border-radius: var(--card-radius);
  box-shadow: 0 18px 32px var(--card-shadow);
  padding: 28px;
  border: 1px solid rgba(0, 0, 0, 0.04);
  overflow: hidden;
}

.product {
  display: flex;
  gap: 28px;
  align-items: flex-start;
}

.left {
  width: 280px;
  flex: 0 0 280px;
  display: flex;
  justify-content: center;
  align-items: center;
}

.product-image {
  width: 240px;
  height: 320px;
  object-fit: contain;
  border-radius: 6px;
  background: transparent;
}

.right {
  flex: 1;
}

.title {
  font-size: 20px;
  margin: 0 0 6px;
  color: var(--navy);
  font-weight: 700;
  line-height: 1.15;
}

.meta-row {
  display: flex;
  align-items: center;
  justify-content: space-between;
  margin-bottom: 12px;
}

.category {
  color: var(--dark-gray);
  font-size: 13px;
  text-transform: lowercase;
}

.rating {
  display: flex;
  align-items: center;
  gap: 10px;
  color: var(--dark-gray);
  font-size: 13px;
}

.rate {
  font-weight: 700;
  color: var(--dark-gray);
}

.dots {
  display: inline-flex;
  align-items: center;
  gap: 6px;
}

.dots span {
  width: 10px;
  height: 10px;
  border-radius: 50%;
  background: rgba(8, 39, 114, 0.08);
  display: inline-block;
  box-shadow: inset 0 -2px 0 rgba(0, 0, 0, 0.03);
}

.dots span.filled {
  background: var(--navy);
  box-shadow: none;
}

.divider {
  height: 1px;
  background: var(--divider);
  margin: 14px 0;
  border-radius: 2px;
}

.description {
  color: #3b4756;
  font-size: 14px;
  line-height: 1.5;
  margin: 0 0 16px 0;
  max-height: 150px;
  overflow: auto;
}

.bottom-row {
  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: 18px;
  margin-top: 6px;
}

.left-bottom .price {
  font-size: 20px;
  color: var(--navy);
  font-weight: 800;
}

.right-bottom {
  display: flex;
  gap: 12px;
  align-items: center;
}

.btn {
  padding: 10px 20px;
  border-radius: 6px;
  font-weight: 700;
  cursor: pointer;
  font-size: 14px;
  min-width: 130px;
  border: none;
}

.btn.buy {
  background: var(--navy);
  color: var(--white);
  box-shadow: 0 10px 20px rgba(0, 39, 114, 0.16);
}

.btn.next {
  background: transparent;
  color: var(--navy);
  border: 2px solid var(--navy);
}

.btn {
  transition:
    transform 140ms ease,
    background 180ms ease,
    color 180ms ease;
}
.btn.buy:hover {
  transform: translateY(-2px);
}

.btn.next:hover {
  transform: translateY(-2px);
  background: var(--navy);
  color: var(--white);
}

.unavailable {
  padding: 34px;
  text-align: center;
  color: var(--dark-gray);
}

.unavailable .sad-illus {
  width: 260px;
  height: 100px;
  margin: 8px auto 12px;
  opacity: 0.6;
}

.unavail-text {
  color: var(--dark-gray);
  font-size: 15px;
  margin: 10px 0;
}

.skeleton {
  background: linear-gradient(
    90deg,
    rgba(247, 249, 252, 1) 0%,
    rgba(238, 246, 255, 1) 50%,
    rgba(247, 249, 252, 1) 100%
  );
  background-size: 200% 100%;
  animation: shimmer 1.2s linear infinite;
  border-radius: 8px;
}

@keyframes shimmer {
  0% {
    background-position: 200% 0;
  }
  100% {
    background-position: -200% 0;
  }
}

.skeleton-image {
  width: 240px;
  height: 320px;
  border-radius: 8px;
}
.skeleton-title {
  width: 58%;
  height: 20px;
  margin-bottom: 8px;
}
.skeleton-category {
  width: 34%;
  height: 12px;
  margin-bottom: 8px;
  opacity: 0.9;
}
.skeleton-rating {
  width: 22%;
  height: 12px;
  margin-bottom: 12px;
}
.skeleton-desc {
  width: 100%;
  height: 12px;
  margin-bottom: 8px;
  opacity: 0.95;
}
.skeleton-price {
  width: 120px;
  height: 28px;
  border-radius: 8px;
}
.skeleton-button {
  width: 140px;
  height: 36px;
  border-radius: 8px;
  margin-left: 8px;
}
.skeleton-button.small {
  width: 120px;
}

@media (max-width: 880px) {
  .card {
    padding: 18px;
    width: calc(100% - 36px);
  }
  .product {
    flex-direction: column;
    align-items: center;
    gap: 18px;
  }
  .left {
    width: 100%;
  }
  .product-image {
    width: 220px;
    height: 300px;
  }
  .right-bottom {
    justify-content: center;
    width: 100%;
  }
}

.btn:focus {
  outline: 3px solid rgba(0, 39, 114, 0.12);
  outline-offset: 3px;
}
</style>
