<template>
  <div class="container my-5">
    <div class="d-flex align-items-end gap-3 flex-wrap mb-4">
      <div>
        <label for="search" class="form-label">Search</label>
        <input
          id="search"
          type="text"
          class="form-control"
          v-model="searchTerm"
          placeholder="search for a lesson..."
          v-on:input="searchLessons"
        />
      </div>

      <div>
        <label for="sortAttribute" class="form-label">Sort By:</label>
        <select
          v-model="sortAttribute"
          class="form-select"
          v-on:change="searchLessons"
        >
          <option value="subject">Subject</option>
          <option value="location">Location</option>
          <option value="price">Price</option>
          <option value="spaces">Spaces</option>
        </select>
      </div>

      <div>
        <label for="sortOrder" class="form-label">Sort Order:</label>
        <select
          v-model="sortOrder"
          class="form-select"
          v-on:change="searchLessons"
        >
          <option value="asc">Ascending</option>
          <option value="desc">Descending</option>
        </select>
      </div>
    </div>

    <main id="main-wrapper">
      <div class="product-container row row-cols-1 row-cols-md-2 row-cols-lg-3 row-gap-3">
        <div class="col" v-for="(lesson, index) in lessons" :key="index">
          <div class="product rounded-8 p-4">
          <i :class="lesson.icon"></i>
          <div class="lesson-details">
            <h2>{{ lesson.subject }}</h2>
            <p>Location: {{ lesson.location }}</p>
            <p>Price: {{ lesson.price }}</p>
            <p>Spaces: {{ lesson.spaces }}</p>
            <button
              class="btn btn-lg success-button"
              @click="addLessonToCart(lesson)"
              :disabled="lesson.availableInventory === 0"
            >
              Add to Cart
            </button>
            <span v-if="lesson.availableInventory === 0">All out!</span>
            <span v-else-if="lesson.availableInventory < 5"
              >Only
              {{ lesson.availableInventory - cartCount(lesson._id) }}
              left!</span
            >
            <!-- <span v-else>Buy Now!</span> -->
          </div>
        </div>
        </div>
      </div>
    </main>
  </div>
</template>

<script>
export default {
  name: "LessonComponent",
  props: ["canAddToCart", "cartCount", "add-to-cart"],
  data() {
    return {
      lessons: [],
      searchTerm: "",
      sortAttribute: "subject",
      sortOrder: "asc",
      baseURL: "https:/backend-coursework-2.vercel.app/",
    };
  },
  created() {
    this.fetchLessons();
  },
  methods: {
    async fetchLessons() {
      try {
        const response = await fetch(`${this.baseURL}collection/lessons`);
        this.lessons = await response.json();
      } catch (error) {
        console.error("Error fetching lessons:", error);
      }
    },
    async searchLessons() {
      //filter lessons from backend
      var query = {
        search: this.searchTerm,
        sort: this.sortAttribute,
        order: this.sortOrder,
      };
      var res = await fetch(`${this.baseURL}search/collection/lessons/`, {
        method: "POST",
        headers: { "Content-Type": "application/json" },
        body: JSON.stringify(query),
      });
      this.lessons = await res.json();
    },
    addLessonToCart(lesson) {
      this.$emit("add-to-cart", lesson);
    },
  },
};
</script>

<style scoped>

.product {
  background: #fff;
  border: 1px solid #ddd;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
  box-sizing: border-box;
}

.lesson-details {
  text-align: center;
}

.success-button {
  background-color: #f7ca00;
  color: black;
  font-size: medium;
  border: none;
  cursor: pointer;
}

.success-button:disabled {
  background-color: #ccc;
  cursor: not-allowed;
}

.product {
  background-color: #e7d37f;
}
</style>
