<template>
  <div>
    <NavBar :user-name="userName" :role-id="roleId" />
    <div class="container-fluid mt-5">
      <div class="row">
        <!-- item list -->
        <div class="col-sm-6 mb-3">
          <h3>Order Products</h3>
          <!-- search box -->
          <div class="col-12">
            <input
              type="text"
              v-model="keyword"
              class="form-control"
              name=""
              id=""
              placeholder="Search here"
              :onchange="searchItem()"
            />
          </div>
          <hr />
          <!-- item list box -->
          <div class="col-12">
            <div class="row">
              <div v-for="item in filteredItems" class="col-sm-6 col-md-4 mb-3">
                <div class="card h-100 shadow-sm">
                  <img
                    v-if="item.image"
                    height="115px"
                    weight="100px"
                    :src="url + item.image"
                    class="card-img-top object-fit-cover"
                    alt="..."
                  />
                  <img
                    v-else
                    src="@/assets/images/gambarDefault.jpg"
                    class="card-img-top object-fit-cover"
                    alt=""
                  />
                  <div class="card-body text-center">
                    <h5 class="card-title">{{ item.name }}</h5>
                    <p class="card-text">
                      <!-- {{ `Rp. ${formatPrice(item.price)}` }} -->
                      Rp {{ item.price.toLocaleString("id-ID") }}
                    </p>
                    <button class="btn btn-success" @click="orderItem(item.id)">
                      Order
                    </button>
                  </div>
                </div>
              </div>
            </div>
          </div>
        </div>
        <!-- ordered item -->
        <div class="col-sm-6 mb-3 order-box">
          <h3>Order List</h3>
          <div v-if="successMessage" class="alert alert-success">
            {{ successMessage }}
          </div>
          <div v-if="errorMessage" class="alert alert-danger">
            {{ errorMessage }}
          </div>
          <div class="mb-3">
            <label for="customerName" class="form-label">Customer Name</label>
            <input
              type="text"
              class="form-control"
              id="customerName"
              placeholder="Customer name"
              v-model="customerName"
            />
          </div>
          <div class="mb-3">
            <label for="tableNumber" class="form-label">Table Number</label>
            <input
              type="number"
              class="form-control"
              id="tableNumber"
              placeholder="Table number"
              v-model="tableNumber"
            />
          </div>
          <hr />
          <div class="item-box">
            <div v-for="order in orders" class="mb-3">
              <div class="d-flex justify-content-between">
                <span>{{ order.name }} {{ `(x${order.quantity})` }}</span>
                <span>Rp {{ order.price.toLocaleString("id-ID") }}</span>
              </div>
              <div class="d-flex justify-content-between">
                <div>
                  <span style="font-size: 15px" class="text-muted"
                    >@{{
                      (order.price / order.quantity).toLocaleString("id-ID")
                    }}</span
                  >
                </div>
                <div>
                  <button
                    class="btn btn-sm btn-outline-secondary ms-1"
                    @click="decreaseQuantity(order.id)"
                  >
                    -
                  </button>
                  <button
                    class="btn btn-sm btn-outline-secondary ms-1"
                    @click="increaseQuantity(order.id)"
                  >
                    +
                  </button>
                  <button
                    class="btn btn-sm btn-danger ms-1"
                    @click="deleteOrder(order.id)"
                  >
                    delete
                  </button>
                </div>
              </div>
              <hr class="dashed-line" />
            </div>
          </div>

          <div class="total-box">
            <div class="d-flex justify-content-between">
              <span>Total</span>
              <span>Rp {{ total.toLocaleString("id-ID") }}</span>
            </div>
          </div>
          <div>
            <button
              class="mt-3 btn btn-success form-control"
              :disabled="processing"
              @click="submitOrder()"
            >
              {{ processing ? "Processing Order..." : "Submit" }}
            </button>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>
<script>
import NavBar from "@/components/NavBar.vue";
import axios from "axios";
import router from "@/router";
import { errorMessages } from "@vue/compiler-core";

export default {
  data() {
    return { 
      userName: "",
      items: [],
      url: `${import.meta.env.VITE_API_BASE_URL}/storage/items/`,
      keyword: "",
      filteredItems: [],
      roleId: "",
      orders: [],
      customerName: "",
      tableNumber: "",
      successMessage: "",
      errorMessage: "",
      processing: false,
    };
  },
  mounted() {
    this.userName = localStorage.getItem("name");
    this.roleId = localStorage.getItem("role_id");

    if (!this.userName || this.userName == null) {
      router.push({ name: "login" });
    }
    if (this.roleId != 4 && this.roleId != 1) {
      router.push({ name: "home" });
    }
    this.getItems();
  },
  methods: {
    getItems() {
      // console.log('get item')
      //   let tembus = this //karena this hanya
      //tembus dari satu fungsi, makannya ditembusin lagi
      // (kalau pakai function biasa)
      axios 
        .get(`${import.meta.env.VITE_API_BASE_URL}/api/item`, {
          headers: { Authorization: `Bearer ${localStorage.getItem("token")}` },
        })
        .then((response) => {
          //   console.log(response);
          //   console.log(response.data.data);
          //   tembus.items = response.data.data //kalau pakau function biasa pakai ini
          this.items = response.data.data; //ini kalau pakai arrow func

          //   router.push({ name: "home" });
        })
        .catch(function (error) {
          console.log(error);
          //   console.log('ini error')
        });
    },
    formatPrice(price) {
      if (price >= 1000 && price < 1000000) {
        return (price / 1000).toFixed(1).replace(/\.0$/, "") + " k"; // Untuk ribuan
      } else if (price >= 1000000) {
        return (price / 1000000).toFixed(1).replace(/\.0$/, "") + "M"; // Untuk jutaan
      }
      return price; // Jika di bawah 1000, tampilkan seperti biasa
    },
    searchItem() {
      // console.log(this.keyword)
      this.filteredItems = this.items.filter((item) =>
        item.name.toLowerCase().includes(this.keyword.toLowerCase())
      );
    },
    orderItem(id) {
      // console.log(id)
      let item = this.filteredItems.filter((item) => item.id == id)[0];
      // console.log(item)
      let orderItem = Object.assign({}, item);
      // console.log(orderItem)
      let findId_inOrders = this.orders
        .map((order) => order.id)
        .indexOf(orderItem.id);
      // console.log(findId_inOrders)
      if (findId_inOrders != -1) {
        //jika item yang dipilih ada di orders, maka tambah quantitiy & kalikan harga
        this.orders[findId_inOrders].quantity++;
        this.orders[findId_inOrders].price += orderItem.price;
      } else {
        //jika item yang dipilih belum ada di orders, maka
        //push item ke dalam array orders dengan quantitiy 1
        orderItem.quantity = 1;
        this.orders.push(orderItem);
      }
    },
    decreaseQuantity(id) {
      let findId_inOrders = this.orders.map((order) => order.id).indexOf(id);

      if (findId_inOrders != -1) {
        if (this.orders[findId_inOrders].quantity > 1) {
          // Kurangi quantity dan harga
          this.orders[findId_inOrders].quantity--;
          this.orders[findId_inOrders].price -= this.filteredItems.find(
            (item) => item.id === id
          ).price;
        } else {
          // Hapus item jika quantity 1
          this.orders.splice(findId_inOrders, 1);
        }
      }
    },
    increaseQuantity(id) {
      let findId_inOrders = this.orders.map((order) => order.id).indexOf(id);

      if (findId_inOrders != -1) {
        // Tambah quantity dan harga
        this.orders[findId_inOrders].quantity++;
        this.orders[findId_inOrders].price += this.filteredItems.find(
          (item) => item.id === id
        ).price;
      }
    },
    deleteOrder(id) {
      let findId_inOrders = this.orders.findIndex((order) => order.id === id);

      if (findId_inOrders !== -1) {
        // Menghapus item dari orders berdasarkan indeks
        this.orders.splice(findId_inOrders, 1);
      }
    },
    submitOrder() {
      // console.log(this.orders)
      if (this.customerName == "" || this.tableNumber == "" || this.orders.length === 0) {
        alert("Customer name, table number, or orders cannot be empty");
        return;
      }
      let order_details = this.orders.map((order) => {
        return {
          id: order.id,
          quantity: order.quantity,
        };
      });
      //   console.log(order_details)
      this.processing = true
      axios 
        .post( 
          `${import.meta.env.VITE_API_BASE_URL}/api/order`,
          {
            customer_name: this.customerName,
            table_no: this.tableNumber,
            items: order_details,
          },
          {
            headers: {
              Authorization: `Bearer ${localStorage.getItem("token")}`,
            },
          }
        )
        .then((response) => {
          //   console.log(response);
          //   console.log(response.data.data);
          //   tembus.items = response.data.data //kalau pakau function biasa pakai ini
          //ini kalau pakai arrow func
          // console.log(response)
          //   router.push({ name: "home" });
          this.successMessage = `Order List ${this.customerName} added successfully!`;
          
          this.resetForm();

          setTimeout(() => {
            this.successMessage = "";
          }, 3500);
        })
        .catch( (error) => {
          console.log(error);
          //   console.log('ini error')
          this.errorMessage = `Error because ${error.response.statusText}!`;
          setTimeout(() => {
            this.errorMessage = "";
          }, 3500);
        })
        .finally(()=> this.processing = false);
    },
    resetForm() {
      this.userName = ""; // Reset name input
      this.tableNumber = ""; // Reset price input
      this.orders = []; // Reset file input
    },
  },
  computed: {
    total() {
      return this.orders
        .map((order) => order.price)
        .reduce((accumulator, currentValue) => accumulator + currentValue, 0);
    },
  },
  components: {
    NavBar,
  },
};
</script>
<style scoped>
.bordered {
  border: solid 1px;
}

.order-box {
  border-left: solid 1px #86a0ac;
}

.item-box {
  font-size: 24px;
}

.total-box {
  font-size: 26px;
  font-weight: bold;
}

.dashed-line {
  border: none;
  border-top: 2px dashed #000;
  /* Menggunakan garis putus-putus */
  margin: 20px 0;
}
</style>
