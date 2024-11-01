<template>
  <NavBar :user-name="userName" :role-id="roleId" />
  <div class="container mt-5">
    <h3 class="mb-3">Edit product</h3>
    <div v-if="successMessage" class="alert alert-success">
      {{ successMessage }}
    </div>
    <div v-if="errorMessage" class="alert alert-danger">
      {{ errorMessage }}
    </div>
    <div class="col-lg-6">
      <form @submit.prevent="editProduct()">
        <label for="name">Name</label>
        <input
          type="text"
          class="form-control mb-3"
          v-model="item.name"
          id="name"
          placeholder="Product name"
        />
        <label for="price">Price</label>
        <input
          type="number"
          class="form-control mb-3"
          v-model="item.price"
          id="price"
          placeholder="Product price"
        />
        <label>Current Image</label>
        <div class="row">
          <div class="col-6">
            <img
              v-if="item.image"
              :src="url + item.image"
              class="object-fit-cover"
              alt=""
              style="max-width: 100px; height: 100px"
            />
            <img
              v-else
              src="@/assets/images/gambarDefault.jpg"
              class="object-fit-cover"
              alt=""
              style="max-width: 100px; height: 100px"
            />
          </div>
        </div>

        <label for="image">Change Image</label>
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
import axios from "axios";
import router from "@/router";
import { errorMessages } from "vue/compiler-sfc";

export default {
  components: {
    NavBar,
  },
  data() {
    return {
      userName: "",
      roleId: "",
      url: `${import.meta.env.VITE_API_BASE_URL}/storage/items/`,
      productId: "",
      item: String,
      successMessage: "",
      errorMessage: "",
      //   name: "",
      //   price: "",
      file: "",
    };
  },
  mounted() {
    // console.log(this.$route) //biar liat data dari url
    this.productId = this.$route.params.productId;
    this.userName = localStorage.getItem("name");
    this.roleId = localStorage.getItem("role_id");

    if (!this.userName || this.userName == null) {
      router.push({ name: "login" });
    }
    if (this.roleId != 4) {
      router.push({ name: "home" });
    }
    this.showItem();
  },
  methods: {
    showItem() {
      axios 
        .get(`${import.meta.env.VITE_API_BASE_URL}/api/item/${this.productId}`, {
          headers: { Authorization: `Bearer ${localStorage.getItem("token")}` },
        })
        .then((response) => {
          // console.log(response.data.data)
          this.item = response.data.data;
          //   this.name = this.item.name; // Mengatur nilai name dari item
          //   this.price = this.item.price;
        })
        .catch(function (error) {
          console.log(error);
        });
    },
    editProduct() {
      // alert('save')
      if (this.item.name == "" || this.item.name == "") {
        alert("Data cannot empty");
        return;
      }

      //   let data = {
      //     //ini buat sebelum pake image (untuk test-test)
      //     name: this.name,
      //     price: this.price,
      //   };

      let formData = new FormData();
      //   console.log(this.item.name)
      //   console.log(this.item.price)
      //   console.log(this.file)
      formData.append("name", this.item.name);
      formData.append("price", this.item.price);
      if (this.file) {
        formData.append("image", this.file);
      }
      formData.append("_method", "patch");

      axios 
        .post(`${import.meta.env.VITE_API_BASE_URL}/api/item/${this.productId}`, formData, {
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
          this.successMessage = `Product ${this.item.name} edited successfully!`;
          //   this.resetForm()
          router.push({ name: "product" });
          setTimeout(() => {
            this.successMessage = "";
          }, 3500); // Waktu dalam milidetik
        })
        .catch((error) => {
          console.log(error);
          this.errorMessage = `Error because ${error.response.statusText}!`;

          setTimeout(() => {
            this.errorMessage = "";
          }, 3500); // Waktu dalam milidetik
        });
    },
    imageChanged(event) {
      //   console.log(event)
      // alert('pilih image')
      this.file = event.target.files[0];
    },
  },
};
</script>
<style></style>
