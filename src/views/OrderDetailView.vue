<template>
  <NavBar :user-name="userName" :role-id="roleId" />
  <div class="container mt-3">
    <div class="table-responsive">
      <table class="table table-bordered">
        <tbody>
          <tr>
            <td>Customer Name : {{ order.customer_name }}</td>
            <td>Table Number : {{ order.table_no }}</td>
            <td>
              Order Date :
              {{
                new Date(order.order_date).toLocaleDateString("id-ID", {
                  day: "numeric",
                  month: "long",
                  year: "numeric",
                })
              }}
            </td>
            <td>Status : {{ order.status }}</td>
          </tr>
          <tr>
            <td>Waitress : {{ order.waitress ? order.waitress.name : "-" }}</td>
            <td>Cashier : {{ order.cashier ? order.cashier.name : "-" }}</td>
            <td>Order Time : {{ order.order_time }}</td>
            <td>
              Grand Total : Rp
              {{ order.total ? order.total.toLocaleString("id-ID") : "" }}
            </td>
          </tr>
        </tbody>
      </table>

      <!-- done if user is chef AND order.status == ordered -->

      <!-- paid if user is cashier of manager AND order.status == done -->
      <div class="mt-3">
        <button
          v-if="order.status == 'ordered' && (roleId == 2 || roleId == 4)"
          class="btn btn-info me-3" @click="setAsDone(order.id)"
        >
          Done
        </button>
        <button
          v-if="order.status == 'done' && (roleId == 3 || roleId == 4)"
          class="btn btn-primary" @click="setAsPaid(order.id)" 
        >
          Paid
        </button>
      </div>
    </div>
    <hr class="my-3" />
    <div>
      <table class="table table-striped">
        <thead>
          <tr>
            <td>#</td>
            <td>Item</td>
            <td>Price</td>
            <td>Quantity</td>
            <td>Total</td>
          </tr>
        </thead>
        <tbody>
          <tr v-for="(item, index) in order.order_detail">
            <td>{{ index + 1 }}</td>
            <td>{{ item.item.name }}</td>
            <td>Rp {{ item.price.toLocaleString("id-ID") }}</td>
            <td>{{ item.quantity }}</td>
            <td>
              Rp {{ (item.price * item.quantity).toLocaleString("id-ID") }}
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
      order: [],
      orderId: "",
    };
  },
  mounted() {
    this.orderId = this.$route.params.orderId;
    this.userName = localStorage.getItem("name");
    this.roleId = localStorage.getItem("role_id");

    if (!this.userName || this.userName == null) {
      router.push({ name: "login" });
    }
    this.getOrder();
  },
  methods: {
    getOrder() {
      axios
        .get("http://localhost:8000/api/order/" + this.orderId, {
          headers: { Authorization: `Bearer ${localStorage.getItem("token")}` },
        })
        .then((response) => {
          //   console.log(response);
          //   console.log(response.data.data);
          //   tembus.items = response.data.data //kalau pakau function biasa pakai ini
          this.order = response.data.data; //ini kalau pakai arrow func

          //   router.push({ name: "home" });
        })
        .catch(function (error) {
          console.log(error);
          //   console.log('ini error')
        });
    },
    setAsDone(id){
        axios
        .get("http://localhost:8000/api/order/" + id + '/set-as-done', {
          headers: { Authorization: `Bearer ${localStorage.getItem("token")}` },
        })
        .then((response) => {
            // console.log(response);
          //   console.log(response.data.data);
          //   tembus.items = response.data.data //kalau pakau function biasa pakai ini
          this.order = response.data.data; //ini kalau pakai arrow func

          //   router.push({ name: "home" });
        })
        .catch(function (error) {
          console.log(error);
          //   console.log('ini error')
        });
        
    },
    setAsPaid(id){
        axios
        .get("http://localhost:8000/api/order/" + id + '/payment', {
          headers: { Authorization: `Bearer ${localStorage.getItem("token")}` },
        })
        .then((response) => {
            // console.log(response);
          //   console.log(response.data.data);
          //   tembus.items = response.data.data //kalau pakau function biasa pakai ini
          this.order = response.data.data; //ini kalau pakai arrow func

          //   router.push({ name: "home" });
        })
        .catch(function (error) {
          console.log(error);
          //   console.log('ini error')
        });
        
    }
  },
};
</script>
<style lang=""></style>
