<template>
  <NavBar :user-name="userName" :role-id="roleId" />

  <div class="container mt-5">
    <h2 class="text-center mb-3">All Order List</h2>
    <div class="table-responsive">
      <table class="table table-striped-columns">
        <thead>
          <tr>
            <th scope="col">#</th>
            <th scope="col">Customer Name</th>
            <th scope="col">Table Number</th>
            <th scope="col">Order Date</th>
            <th scope="col">Time</th>
            <th scope="col">Total</th>
            <th scope="col">Status</th>
            <th scope="col">Waitress</th>
            <th scope="col">Cashier</th>
            <th scope="col">Action</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="(order, index) in orders">
            <th scope="row">{{ index + 1 }}</th>
            <td>{{ order.customer_name }}</td>
            <td>{{ order.table_no }}</td>
            <td>
              {{
                new Date(order.order_date).toLocaleDateString("id-ID", {
                  day: "numeric",
                  month: "long",
                  year: "numeric",
                })
              }}
            </td>
            <td>{{ order.order_time }}</td>
            <td>Rp {{ order.total.toLocaleString("id-ID") }}</td>
            <td>{{ order.status }}</td>
            <td>{{ order.waitress ? order.waitress.name : "" }}</td>
            <td>{{ order.cashier ? order.cashier.name : "" }}</td>
            <td>
              <RouterLink
                :to="{ name: 'orderDetail', params: { orderId: order.id } }"
                >Detail
              </RouterLink>
            </td>
          </tr>
        </tbody>
      </table>
    </div>
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
      orders: [],
    };
  },
  mounted() {
    this.userName = localStorage.getItem("name");
    this.roleId = localStorage.getItem("role_id");

    if (!this.userName || this.userName == null) {
      router.push({ name: "login" });
    }
    this.getOrders();
  },
  methods: {
    getOrders() {
      axios 
        .get(`${import.meta.env.VITE_API_BASE_URL}/api/order`, {
          headers: { Authorization: `Bearer ${localStorage.getItem("token")}` },
        })
        .then((response) => {
          //   console.log(response);
          //   console.log(response.data.data);
          //   tembus.items = response.data.data //kalau pakau function biasa pakai ini
          this.orders = response.data.data; //ini kalau pakai arrow func

          //   router.push({ name: "home" });
        })
        .catch(function (error) {
          console.log(error);
          //   console.log('ini error')
        });
    },
  },
};
</script>
<style lang=""></style>
