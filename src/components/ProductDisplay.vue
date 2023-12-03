<template>
  <div
    class="container"
    :class="
      isLoading
        ? 'bg-white'
        : product.data.category === 'men\'s clothing'
        ? 'bg-men'
        : 'bg-women'
    "
  >
    <!-- display loading when data is fetching -->
    <div v-if="isLoading">
      <div class="product-container-spinner">
        <div class="spinner"></div>
      </div>
    </div>

    <!-- display product info -->
    <div
      v-else
      class="container"
      :class="
        !isProductAvailable
          ? 'bg-none'
          : product.data.category === 'men\'s clothing'
          ? 'bg-men'
          : 'bg-women'
      "
    >
      <div class="overlay"></div>
      <div class="card">
        <div v-if="!isProductAvailable" class="product-unavailable-container">
          <div class="product-details">
            <p>This product is not available.</p>
            <div class="cta">
              <button type="button" @click="getProduct()" class="cta-next-none">
                Next Product
              </button>
            </div>
          </div>
        </div>
        <div v-else class="product-container">
          <div class="product-thumbnail">
            <img :src="product.data.image" alt="" class="product-image" />
          </div>
          <div class="product-details">
            <div class="top">
              <h3
                :class="
                  product.data.category === 'men\'s clothing'
                    ? 'font-men'
                    : 'font-women'
                "
                class="title"
              >
                {{ product.data.title }}
              </h3>
              <div class="sub-title">
                <span>{{ product.data.category }}</span>
                <div class="rating">
                  <span>{{ product.data.rating.rate }}/5</span>
                  <div class="rating">
                    <span
                      v-for="i in 5"
                      :key="i"
                      :class="[
                        'circle',
                        i <= product.data.rating.rate ? ratingColorClass : '',
                      ]"
                    ></span>
                  </div>
                </div>
              </div>
              <div class="description">
                <p>{{ product.data.description }}</p>
              </div>
            </div>
            <div class="bottom">
              <span
                :class="
                  product.data.category === 'men\'s clothing'
                    ? 'font-men'
                    : 'font-women'
                "
                class="price"
                >${{ product.data.price }}</span
              >
              <div class="cta">
                <button
                  type="button"
                  :class="
                    product.data.category === 'men\'s clothing'
                      ? 'cta-buy-men'
                      : 'cta-buy-women'
                  "
                  class="cta-buy"
                >
                  Buy Now
                </button>
                <button
                  type="button"
                  @click="getProduct()"
                  :class="
                    !isProductAvailable
                      ? 'cta-next-none'
                      : product.data.category === 'men\'s clothing'
                      ? 'cta-next-men'
                      : 'cta-next-women'
                  "
                >
                  Next Product
                </button>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "ProductDisplay",
  data() {
    return {
      /* return fetched data */
      isLoading: false,
      index: 0,
      isProductAvailable: false,
      product: {},
    };
  },
  computed: {
    /* compute rating circle color based on category */
    ratingColorClass() {
      return this.product.data.category === "men's clothing"
        ? "bg-rating-men"
        : "bg-rating-women";
    },
  },
  /* fetching method from API */
  methods: {
    async callProductAPI() {
      const response = await fetch(
        `https://fakestoreapi.com/products/${this.index}`
      );

      if (!response.ok) {
        /* handling fetch fail */
        throw new Error(`API request failed with status ${response.status}`);
      }

      const data = await response.json();
      return data;
    },
    async getProduct() {
      this.isLoading = true;

      if (this.index !== 20) {
        this.index++;
      } else {
        this.index = 1;
      }

      try {
        const data = await this.callProductAPI();

        if (
          data.category === "men's clothing" ||
          data.category === "women's clothing"
        ) {
          this.product = { data };
          this.isProductAvailable = true;
        } else {
          this.isProductAvailable = false;
        }
      } catch (err) {
        /* handle error fetching */
        console.error("Error fetching product:", err);
      } finally {
        this.isLoading = false;
      }
    },
  },
  mounted() {
    this.getProduct();
  },
};
</script>
