<template>
  <div class="content">
    <div
      class="section"
      :class="{
        'bg-light-blue': isMan,
        'bg-pink': isWomen,
        'bg-grey': isAvailable,
      }"
    ></div>
    <div class="loader" v-if="isLoading"></div>
    <div class="container" v-else>
      <div class="card">
        <div class="card-body" v-if="!isAvailable">
          <div class="card-image">
            <img :src="product.image" alt="this product" />
          </div>
          <div class="card-text">
            <div class="card-text-header">
              <p
                class="card-text-title"
                :class="{ 'text-purple': isWomen, 'text-strong-blue': isMan }"
              >
                {{ product.title }}
              </p>
              <div class="card-text-subtitle">
                <span>{{ product.category }}</span>
                <div>
                  <span>{{ product.rating.rate }}&sol;5&nbsp;</span>
                  <div class="rating">
                    <span
                      v-for="i in 5"
                      :key="i"
                      :class="{ dot: true, active: i <= computedRating, 'border-purple': isWomen, 'border-strong-blue': isMan }"
                    ></span>
                  </div>
                </div>
              </div>
            </div>
            <hr />
            <div class="card-text-description">
              <p>{{ modifiedDescription(product.description) }}</p>
            </div>
            <hr />
            <div class="card-footer">
              <div class="card-text-price" :class="{ 'text-purple': isWomen, 'text-strong-blue': isMan }">
                <b>&dollar;{{ product.price }}</b>
              </div>
              <div class="card-button">
                <button class="btn-fill" :class="{ 'bg-purple': isWomen, 'bg-strong-blue': isMan }">buy now</button>
                <button class="btn-outline" :class="{ 'text-purple': isWomen, 'text-strong-blue': isMan }" @click="nextProductDisplay">next product</button>
              </div>
            </div>
          </div>
        </div>
        <div class="card-body bg-sad-face" v-else>
          <div class="card-text-unavailable">
            <p>This product is unavailable to show</p>
            <button class="btn-outline text-light-black" @click="nextProductDisplay">next product</button>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>
<script>
export default {
  name: "productCard",
  data() {
    return {
      currentIndex: 1,
      isLoading: false,
      product: {
        image: '',
        title: '',
        category: '',
        description: '',
        price: 0,
        rating: {
          rate: 0,
        },
      },
      isWomen: false,
      isMan: false,
      isAvailable: false
    };
  },
  computed: {
    computedRating() {
      return Math.round(this.product.rating.rate);
    },
  },
  methods: {
    callApi() {
      this.isLoading = true

      fetch("https://fakestoreapi.com/products/"+ this.currentIndex)
      .then((response) => response.json())
      .then((data) => {
        this.isMan = false
        this.isWomen = false
        this.isAvailable = false
        this.product = data
        switch (this.product.category) {
            case 'men\'s clothing':
                this.isMan = !this.isMan
                break;
            case 'women\'s clothing':
                this.isWomen = !this.isWomen
                break;
            default:
                this.isAvailable = !this.isAvailable
                break;
        }
        this.isLoading = false

      })
      .catch((err) => {
        console.log(err)
        this.isLoading = false
      })
    },
    nextProductDisplay() {
      const self = this
      
      let newCurrentIndex = self.currentIndex >= 20 ? 0 : self.currentIndex
      this.currentIndex = newCurrentIndex + 1

      self.callApi()      
    },
    modifiedDescription(word) {
      let maxLength = 400

      if (word.length > maxLength) {
        return word.substring(0, maxLength) + '...';
      } else {
        return word
      }
    }
  },
  mounted() {
    this.callApi()
  },
};
</script>
<style scoped>
  @import url('../assets/style/page.css');
</style>
