<template>
  <div class="products">
    <product-list-item v-for="item in products" :product-item="item" @itemImageLoad="itemImageLoad"/>
  </div>
</template>

<script>
import ProductListItem from "@/components/context/productList/ProductListItem";

export default {
  name: "ProductList",
  components: {ProductListItem},
  props: {
    products: {
      type: Array,
      default() {
        return [];
      }
    }
  },
  methods: {
    itemImageLoad() {
      this.$emit('itemImageLoad');
    }
  }
}
</script>

<style scoped>
.products {
  display: flex;
  flex-wrap: wrap;
  justify-content: space-around;
  padding: 2px;
}
</style>
