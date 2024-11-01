<template lang="">
  <div class="container mt-5">
    <div class="row vh-100 justify-content-center align-items-center">
      <div class="col-md-4">
        <div class="card">
          <div class="card-header text-center">
            <h4>Login</h4>
          </div>
          <div class="card-body">
            <form @submit.prevent>
              <div class="mb-3">
                <label for="email" class="form-label">Email address</label>
                <input
                  type="email"
                  v-model="email"
                  class="form-control"
                  id="email"
                  placeholder="Enter your email"
                  required
                />
              </div>
              <div class="mb-3">
                <label for="password" class="form-label">Password</label>
                <input
                  type="password"
                  v-model="password"
                  class="form-control"
                  id="password"
                  placeholder="Enter your password"
                  required
                />
              </div>

              <button
                @click="login()"
                class="btn btn-primary w-100 form-control"
              >
                Login
              </button>
            </form>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>
<script>
import axios from "axios";
import router from "@/router";


export default {
  data() {
    return {
      email: "",
      password: "",
    };
  },
  methods: {
    login() {
      // console.log(this.email)
      // console.log(this.password)
      axios 
        .post(`${import.meta.env.VITE_API_BASE_URL}/api/auth/login`, {
          email: this.email,
          password: this.password,
        })
        .then(function (response) {
          //   console.log(response);
          localStorage.setItem("email", response.data.data.email);
          localStorage.setItem("name", response.data.data.name);
          localStorage.setItem("role_id", response.data.data.role_id);
          localStorage.setItem("token", response.data.data.token);

          router.push({ name: "home" });
        })
        .catch(function (error) {
          console.log(error);
          //   console.log('ini error')
        });
    },
  },
  mounted() {
    this.userName = localStorage.getItem("name");
    if (this.userName || this.userName != null) {
      router.push({ name: "home" });
    }
  },
};
</script>
<style lang=""></style>
