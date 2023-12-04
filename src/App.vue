<template>
  <div id="app">
    <div v-if="loading">
      <!-- Loading Element -->
      <p>Loading...</p>
    </div>
    <div v-else>
      <div v-if="category === 'men'">
        <div class="page-men">
          <!-- Men Section Design -->
          <h2>Men's Section</h2>
          <ul>
            <li v-for="product in products" :key="product.id">
              {{ product.title }}
            </li>
          </ul>
        </div>
      </div>
      <div v-else-if="category === 'women'">
        <div class="page-women">
          <!-- Women Section Design -->
          <h2>Women's Section</h2>
          <ul>
            <li v-for="product in products" :key="product.id">
              {{ product.title }}
            </li>
          </ul>
        </div>
      </div>
      <div v-else>
        <div class="page-unavailable">
          <!-- Unavailable Product Design -->
          <h2>Unavailable Product</h2>
          <p>Sorry, no products available in this category.</p>
        </div>
      </div>
      <button @click="getNextProducts" :disabled="loading">Next Product</button>
    </div>
  </div>
</template>


<script>
export default {
  data() {
    return {
      products: [],
      index: 1,
      category: '',
      loading: false, // New loading state
    };
  },
  methods: {
    async getNextProducts() {
      this.index = this.index === 20 ? 1 : this.index + 1;
      await this.fetchProducts();
    },
    async fetchProducts() {
      try {
        this.loading = true; // Set loading to true when making API call
        const response = await this.$axios.get(`https://fakestoreapi.com/products/${this.index}`);
        const product = response.data;

        // Check category and update data accordingly
        if (product.category === "men's clothing") {
          this.category = 'men';
          this.products = [product];
        } else if (product.category === "women's clothing") {
          this.category = 'women';
          this.products = [product];
        } else {
          this.category = 'unavailable';
          this.products = [];
        }
      } catch (error) {
        console.error('Error fetching products:', error);
      } finally {
        this.loading = false; // Set loading to false when API call is complete
      }
    },
  },
  mounted() {
    this.fetchProducts();
  },
};
</script>


<style>
/* Vanilla CSS with Color Palette variables */
.page-men {
  background-color: var(--men-background-color);
  color: var(--men-text-color);
}

.page-women {
  background-color: var(--women-background-color);
  color: var(--women-text-color);
}

.page-unavailable {
  background-color: var(--unavailable-background-color);
  color: var(--unavailable-text-color);

  :root {
  --men-background-color: #D6E6FF;  /* Blue */
  --men-text-color: #002772;       /* White */

  --women-background-color: #FDE2FF;  /* Red */
  --women-text-color:#720060;        /* White */

  --unavailable-background-color: #3F3F3F;  /* Gray */
  --unavailable-text-color: #3F3F3F;       /* White */
}

}
</style>
