<template>
  <NavBar :user-name="userName" :role-id="roleId" />
  <div class="container">
    <h2>Product List</h2>
    <RouterLink to="product-add" class="btn btn-success"
      >Add Product</RouterLink
    >

    <!-- Input Pencarian -->
    <div class="text-center">
      <label class="mt-3" for="search">Search Product</label>
      <input
        type="text"
        class="form-control my-3"
        placeholder="Search product by name"
        v-model="searchQuery"
        id="search"
      />
    </div>

    <div class="row my-5">
      <table class="table table-striped">
        <thead>
          <tr>
            <th scope="col">#</th>
            <th scope="col">Name</th>
            <th scope="col">Price</th>
            <th scope="col">Image</th>
            <th scope="col">Action</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="(item, index) in paginatedItems" :key="item.id">
            <th scope="row">{{ currentPage * itemsPerPage + index + 1 }}</th>
            <td>{{ item.name }}</td>
            <td>Rp {{ item.price.toLocaleString("id-ID") }}</td>
            <td>
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
            </td>
            <td>
              <RouterLink
                :to="{ name: 'productUpdate', params: { productId: item.id } }"
                >Edit
              </RouterLink>
            </td>
          </tr>
        </tbody>
      </table>
    </div>

    <!-- Pagination Controls -->
    <nav>
      <ul class="pagination">
        <li class="page-item" :class="{ disabled: currentPage === 0 }">
          <button class="page-link" @click="prevPage">Previous</button>
        </li>
        <li
          class="page-item"
          v-for="page in totalPages"
          :key="page"
          :class="{ active: page === currentPage + 1 }"
        >
          <button class="page-link" @click="goToPage(page - 1)">
            {{ page }}
          </button>
        </li>
        <li
          class="page-item"
          :class="{ disabled: currentPage === totalPages - 1 }"
        >
          <button class="page-link" @click="nextPage">Next</button>
        </li>
      </ul>
    </nav>
  </div>
</template>

<script>
import NavBar from "@/components/NavBar.vue";
import axios from "axios";
import router from "@/router";

export default {
  components: {
    NavBar,
  },
  data() {
    return {
      userName: "",
      roleId: "",
      items: [],
      url: "http://localhost:8000/storage/items/",
      currentPage: 0, // Halaman aktif
      itemsPerPage: 10, // Jumlah item per halaman
      searchQuery: "", // Input pencarian
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
    this.getItems();
  },
  computed: {
    // Menghitung item yang akan ditampilkan sesuai pencarian
    filteredItems() {
      if (this.searchQuery) {
        return this.items.filter((item) =>
          item.name.toLowerCase().includes(this.searchQuery.toLowerCase())
        );
      }
      return this.items;
    },
    // Menghitung item yang akan ditampilkan pada halaman aktif
    paginatedItems() {
      const start = this.currentPage * this.itemsPerPage;
      const end = start + this.itemsPerPage;
      return this.filteredItems.slice(start, end); // Menggunakan filteredItems
    },
    // Menghitung total halaman berdasarkan filteredItems
    totalPages() {
      return Math.ceil(this.filteredItems.length / this.itemsPerPage);
    },
  },
  methods: {
    getItems() {
      axios
        .get("http://localhost:8000/api/item", {
          headers: { Authorization: `Bearer ${localStorage.getItem("token")}` },
        })
        .then((response) => {
          this.items = response.data.data;
        })
        .catch(function (error) {
          console.log(error);
          if(error.response.status == 401){
            localStorage.removeItem('token')
            localStorage.removeItem('email')
            localStorage.removeItem('name')
            localStorage.removeItem('role_id')
            router.push({name:'login'})
          }
        });
    },
    // Pindah ke halaman sebelumnya
    prevPage() {
      if (this.currentPage > 0) {
        this.currentPage--;
      }
    },
    // Pindah ke halaman berikutnya
    nextPage() {
      if (this.currentPage < this.totalPages - 1) {
        this.currentPage++;
      }
    },
    // Pindah ke halaman tertentu
    goToPage(page) {
      this.currentPage = page;
    },
  },
};
</script>
