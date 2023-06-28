<script>
import SkeletonDisplay from './SkeletonDisplay.vue'

const API_URL = 'https://fakestoreapi.com/products'
export default {
  components: {
    'skeleton-display': SkeletonDisplay
  },
  data() {
    return {
      index: 0,
      isLoading: false,
      isProductAvailable: false,
      productItem: []
    }
  },
  methods: {
    async getProductByID() {
      const result = fetch(`${API_URL}/${this.index}`).then((res) => res.json())
      return result
    },
    async getNextProduct() {
      this.isLoading = true
      if (this.index !== 20) {
        this.index += 1
      } else {
        this.index = 1
      }

      const data = await this.getProductByID()
      console.log(data)
      if (data.category === "men's clothing" || data.category === "women's clothing") {
        this.productItem = { data }
        this.isProductAvailable = true
      } else {
        this.isProductAvailable = true
      }

      this.isLoading = false
    }
  },
  mounted() {
    this.getNextProduct()
  }
}
</script>

<template>
  <div v-if="isLoading" class="card-skeleton">
    <skeleton-display />
  </div>
  <div
    v-else
    class="container"
    :class="
      !isProductAvailable
        ? 'bg-gray'
        : productItem.data.category === 'men\'s clothing'
        ? 'bg-blue'
        : 'bg-pink'
    "
  >
    <img class="patern" src="../assets/bg-pattern.svg" alt="" />
    <div class="contain-product">
      <div v-if="!isProductAvailable" class="product-unavailable-container">
        <div class="overlay">
          <img src="../assets/not-found.svg" alt="unavailable product" />
        </div>
        <div class="content">
          <p>This product is unavailable to show</p>
          <div class="btn-available">
            <button type="button" @click="getNextProduct()" class="btn-available-next">
              Next Product
            </button>
          </div>
        </div>
      </div>
      <div v-else>
        <div class="img-product">
          <img :src="productItem.data.image" :alt="productItem.data.title" />
        </div>
        <div class="content">
          <div class="wrapper">
            <h3
              :class="
                productItem.data.category === 'men\'s clothing' ? 'font-navy' : 'font-magenta'
              "
              class="title"
            >
              {{ productItem.data.title }}
            </h3>
            <div class="sub-title">
              <span>{{ productItem.data.category }}</span>
              <!-- rating -->
              <div class="rating">
                <span>{{ productItem.data.rating.rate }}/5</span>
                <div v-for="i in 5" :key="i" class="ctr-rating">
                  <div
                    class="circle"
                    :class="{ filled: i <= this.productItem.data?.rating?.rate }"
                  ></div>
                </div>
              </div>
            </div>
            <p class="desc">
              {{ productItem.data.description }}
            </p>
          </div>
          <div class="wrapper2">
            <p
              :class="
                productItem.data.category === 'men\'s clothing' ? 'font-navy' : 'font-magenta'
              "
              class="price"
            >
              ${{ productItem.data.price }}
            </p>
            <div class="btn">
              <button
                :class="productItem.data.category === 'men\'s clothing' ? 'bg-navy' : 'bg-magenta'"
                class="btn-buy"
              >
                Buy now
              </button>
              <button
                @click="getNextProduct()"
                :class="
                  productItem.data.category === 'men\'s clothing'
                    ? 'border-navy font-navy'
                    : 'border-magenta font-magenta'
                "
                class="btn-next"
              >
                Next product
              </button>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<style>
.rating {
  display: flex;
  align-items: center;
}

.ctr-rating {
  margin-right: 5px;
}

.circle {
  width: 20px;
  height: 20px;
  border-radius: 50%;
  border: 1px solid #ddd;
}

.filled {
  background-color: blue;
}
</style>
