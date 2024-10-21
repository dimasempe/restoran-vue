<template>
  <NavBar :user-name="userName" :role-id="roleId" />
  <div class="container mt-5">
    <div class="mb-3 w-50">
      <label for="month" class="form-label">Month</label>
      <select
        class="form-control"
        id="month"
        v-model="month"
        @change="getReport()"
      >
        <option value="" disabled selected>Choose Month Period</option>
        <option v-for="month in months" :value="month.value">
          {{ month.name }}
        </option>
      </select>
    </div>
    <!-- order count -->
    <div class="row">
      <div class="col-sm-4">
        <div
          class="box"
          style="
            border: solid 1px #007bff;
            background-color: #e9f5ff;
            border-radius: 5px;
            color: #007bff;
            padding: 10px;
          "
        >
          <label> Order Count : </label>
          <label>{{ data.ordersCount }}</label>
        </div>
      </div>
      <div class="col-sm-4">
        <div
          class="box"
          style="
            border: solid 1px #fd7e14;
            background-color: #fff4e6;
            border-radius: 5px;
            color: #fd7e14;
            padding: 10px;
          "
        >
          <label>Minimum Payment :</label>
          <label>Rp {{ data.minPayment }}</label>
        </div>
      </div>
      <div class="col-sm-4">
        <div
          class="box"
          style="
            border: solid 1px #28a745;
            background-color: #e6f9ed;
            border-radius: 5px;
            color: #28a745;
            padding: 10px;
          "
        >
          <label> Maximum Payment :</label>
          <label>Rp {{ data.maxPayment }}</label>
        </div>
      </div>
    </div>

    <div class="row mt-3">
      <div class="col-sm-4">
        <div
          class="box"
          style="
            border: solid 1px #ff5733; /* Merah */
            background-color: #ffe5e0;
            border-radius: 5px;
            color: #ff5733;
            padding: 10px;
          "
        >
          <label>Bakmie Affordable :</label>
          <label>{{ data.totalAfford }}</label>
        </div>
      </div>

      <div class="col-sm-4">
        <div
          class="box"
          style="
            border: solid 1px #c70039; /* Merah Tua */
            background-color: #f0e5e9;
            border-radius: 5px;
            color: #c70039;
            padding: 10px;
          "
        >
          <label>Bakmie Premium :</label>
          <label>{{ data.totalPremium }}</label>
        </div>
      </div>

      <div class="col-sm-4">
        <div
          class="box"
          style="
            border: solid 1px #900c3f; /* Ungu Tua */
            background-color: #f5e5f5;
            border-radius: 5px;
            color: #900c3f;
            padding: 10px;
          "
        >
          <label>Total Teh :</label>
          <label>{{ data.totalTeh }}</label>
        </div>
      </div>
    </div>

    <div class="row mt-3">
      <div class="col-sm-4">
        <div
          class="box"
          style="
            border: solid 1px #581845; /* Ungu */
            background-color: #f2e5f2;
            border-radius: 5px;
            color: #581845;
            padding: 10px;
          "
        >
          <label>Total Dimsum :</label>
          <label>{{ data.totalDimsum }}</label>
        </div>
      </div>

      <div class="col-sm-4">
  <div
    class="box"
    style="
      border: solid 1px #6f42c1; /* Ungu */
      background-color: #e8d8f2; 
      border-radius: 5px;
      color: #6f42c1;
      padding: 10px;
    "
  >
    <label>Total Pangsit :</label>
    <label>{{ data.totalPangsit }}</label>
  </div>
</div>

      <div class="col-sm-4">
        <div
          class="box"
          style="
            border: solid 1px #33ccff; /* Cyan */
            background-color: #e0f7fa;
            border-radius: 5px;
            color: #33ccff;
            padding: 10px;
          "
        >
          <label>Total Gohyong :</label>
          <label>{{ data.totalGohyong }}</label>
        </div>
      </div>
    </div>

    <hr class="my-5" />
    <div class="row">
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
            <tr v-for="(order, index) in data.orders">
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
              <td>{{ order.waitress ? order.waitress.name : "-" }}</td>
              <td>{{ order.cashier ? order.cashier.name : "-" }}</td>
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
      months: [
        { value: 1, name: "January" },
        { value: 2, name: "February" },
        { value: 3, name: "March" },
        { value: 4, name: "April" },
        { value: 5, name: "May" },
        { value: 6, name: "June" },
        { value: 7, name: "July" },
        { value: 8, name: "August" },
        { value: 9, name: "September" },
        { value: 10, name: "October" },
        { value: 11, name: "November" },
        { value: 12, name: "December" },
      ],
      month: "",
      data: {
        ordersCount: 0,
        minPayment: 0,
        maxPayment: 0,
      },
    };
  },
  mounted() {
    this.userName = localStorage.getItem("name");
    this.roleId = localStorage.getItem("role_id");

    if (!this.userName || this.userName == null) {
      router.push({ name: "login" });
    }
    this.getReport();
  },
  methods: {
    getReport() {
      axios
        .get("http://localhost:8000/api/order-report?month=" + this.month, {
          headers: { Authorization: `Bearer ${localStorage.getItem("token")}` },
        })
        .then((response) => {
          console.log(response);
          //   console.log(response.data.data);
          //   tembus.items = response.data.data //kalau pakau function biasa pakai ini
          this.data = response.data.data; //ini kalau pakai arrowfunc

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
<style scoped>
.box label {
  display: block;
}
</style>
