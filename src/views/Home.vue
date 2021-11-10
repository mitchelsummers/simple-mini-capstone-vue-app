<template>
  <div class="home">
    <h1>{{ message }}</h1>

    <h3>New Product</h3>

    name:
    <input type="text" v-model="newProductParams.name" />
    description:
    <input type="text" v-model="newProductParams.description" />
    price:
    <input type="text" v-model="newProductParams.price" />
    image_url:
    <input type="text" v-model="newProductParams.image_url" />
    <button v-on:click="createProduct()">Create Product</button>

    <h3>All Products</h3>

    <div v-for="product in products" :key="product.id">
      <img :src="product.image_url" :all="product.name" />
      <h4>{{ product.name }}</h4>
      <p>Price: ${{ product.price }}</p>
      <button v-on:click="showProduct(product)">More Info</button>
      <div>------------------------------------------------</div>
    </div>
    <dialog id="product-description">
      <form method="dialog">
        <h4>
          Name:
          <input type="text" v-model="currentProduct.name" />
        </h4>
        <p>
          Price: $
          <input type="text" v-model="currentProduct.price" />
        </p>
        <p>
          Description:
          <input type="text" v-model="currentProduct.description" />
        </p>
        <p>
          ImageURL:
          <input type="text" v-model="currentProduct.image_url" />
        </p>
        <button v-on:click="updateProduct(currentProduct)">Update Product</button>
        <button v-on:click="destroyProduct(currentProduct)">Delete Product</button>
        <button>close</button>
      </form>
    </dialog>
  </div>
</template>

<style>
img {
  width: 250px;
}
</style>

<script>
import axios from "axios";

export default {
  data: function () {
    return {
      message: "Welcome to Products!",
      products: [],
      newProductParams: {},
      name: "",
      description: "",
      price: "",
      currentProduct: {},
    };
  },
  created: function () {
    this.indexProducts();
  },
  methods: {
    indexProducts: function () {
      axios.get("http://localhost:3000/products").then((response) => {
        this.products = response.data;
        console.log("All Products", this.products);
      });
    },
    createProduct: function () {
      axios.post("http://localhost:3000/products/", this.newProductParams).then((response) => {
        console.log(response.data);
        this.products.push(response.data);
      });
      this.newProductParams.name = "";
      this.newProductParams.description = "";
      this.newProductParams.price = "";
      this.newProductParams.image_url = "";
    },
    showProduct: function (product) {
      this.currentProduct = product;
      console.log(product);
      document.querySelector("#product-description").showModal();
    },
    updateProduct: function (product) {
      axios.patch("http://localhost:3000/products/" + product.id, product).then((response) => {
        console.log("Product Updated", response.data);
      });
    },
    destroyProduct: function (product) {
      axios.delete("http://localhost:3000/products/" + product.id).then((response) => {
        console.log("Product Deleted", response.data);
        var index = this.products.indexOf(product);
        this.recipes.splice(index, 1);
      });
    },
  },
};
</script>
