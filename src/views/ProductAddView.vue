<template>
  <NavBar :user-name="userName" :role-id="roleId" />
  <div class="container mt-5">
    <h3 class="mb-3">Add new product</h3>
    <div v-if="successMessage" class="alert alert-success">
      {{ successMessage }}
    </div>
    <div v-if="errorMessage" class="alert alert-danger">
      {{ errorMessage }}
    </div>
    <div class="col-lg-6">
      <form @submit.prevent="createProduct()">
        <label for="name">Name</label>
        <input
          type="text"
          class="form-control mb-3"
          v-model="name"
          id="name"
          placeholder="Product name"
        />
        <label for="price">Price</label>
        <input
          type="number"
          class="form-control mb-3"
          v-model="price"
          id="price"
          placeholder="Product price"
        />
        <label for="image">Image</label>
        <input
          type="file"
          class="form-control mb-3"
          @change="imageChanged($event)"
          id="image"
        />
        <button class="btn btn-success form-control" type="submit">Save</button>
      </form>
    </div>
  </div>
</template>
<script>
import NavBar from "@/components/NavBar.vue";
import router from "@/router";
import axios from "axios";
import { errorMessages } from "vue/compiler-sfc";

export default {
  components: {
    NavBar,
  },
  data() {
    return {
      userName: "",
      roleId: "",
    //   items: [],
      url: "http://localhost:8000/storage/items/",
      name: "",
      price: "",
      successMessage: "",
      errorMessage: "",
      file: "",
    };
  },
  mounted() {
    this.userName = localStorage.getItem("name");
    this.roleId = localStorage.getItem("role_id");

    if (!this.userName || this.userName == null) {
      router.push({ name: "login" });
    }
    if (this.roleId != 4) {
      router.push({ name: "home" });
    }
  },
  methods: {
    createProduct() {
      // alert('save')
      if (this.name == "" || this.price == "") {
        alert("Data cannot empty");
        return;
      }

      let data = {
        //ini buat sebelum pake image (untuk test-test)
        name: this.name,
        price: this.price,
      };

      let formData = new FormData();
      formData.append("name", this.name);
      formData.append("price", this.price);
      formData.append("image", this.file);

      axios
        .post("http://localhost:8000/api/item", formData, {
          headers: {
            Authorization: `Bearer ${localStorage.getItem("token")}`,
          },
        })
        .then((response) => {
          //   console.log(response);
          //   console.log(response.data.data);
          //   tembus.items = response.data.data //kalau pakau function biasa pakai ini
        //   this.items = response.data.data; //ini kalau pakai arrow func
          //   router.push({name:'product'})
          this.successMessage = `Product ${this.name} added successfully!`;

          this.resetForm()

          setTimeout(() => {
            this.successMessage = "";
          }, 3500); // Waktu dalam milidetik
        })
        .catch((error)=> {
          console.log(error);
          //   console.log('ini error')
          this.errorMessage = `Error because ${error.response.statusText}!`;

          setTimeout(() => {
            this.errorMessage = "";
          }, 3500); // Waktu dalam milidetik
        });
    },
    imageChanged(event) {
      // console.log(event.target.files[0])
      // alert('pilih image')
      this.file = event.target.files[0];
    },
    resetForm() {
      this.name = ""; // Reset name input
      this.price = ""; // Reset price input
      this.file = ''; // Reset file input
      document.getElementById("image").value = ""; // Reset file input field in the DOM
    },
  },
};
</script>
<style></style>
