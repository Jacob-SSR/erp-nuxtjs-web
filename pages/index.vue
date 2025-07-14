<template>
  <div
    class="min-h-screen flex items-center justify-center bg-gradient-to-t from-blue-900 to-blue-100"
  >
    <div class="w-full max-w-md p-8 space-y-6 bg-white shadow-lg rounded-lg">
      <h2 class="text-3xl font-bold text-gray-900 text-center">
        Sign In to Nuxt ERP
      </h2>
      <form class="mt-8 space-y-6" @submit.prevent="handleSubmit">
        <div class="rounded-md shadow-sm space-y-px">
          <div>
            <div>Username</div>
            <input
              v-model="username"
              type="text"
              required
              class="form-control"
              placeholder="Username"
            />
          </div>
          <div class="mt-5">
            <div>Password</div>
            <input
              v-model="password"
              type="password"
              required
              class="form-control"
              placeholder="Password"
            />
          </div>
        </div>
        <div class="flex justify-between">
          <div class="flex">
            <input
              id="remember-me"
              type="checkbox"
              class="h-4 w-4 text-indigo-600 focus:ring-indigo-500 rounded"
            />
            <label for="remember-me" class="ml-2 block text-sm text-gray-900"
              >Remember me</label
            >
          </div>
          <div class="text-sm">
            <a
              href="#"
              class="font-medium text-indigo-600 hover:text-indigo-500"
              >Forgot Your Password</a
            >
          </div>
        </div>
        <div>
          <button type="submit" class="btn-full">Sign In</button>
        </div>
      </form>
    </div>
  </div>
</template>

<script setup>
import Swal from "sweetalert2";
import config from "@/config";

const username = ref("");
const password = ref("");

const handleSubmit = async () => {
  try {
    if (!username.value || !password.value) {
      Swal.fire({
        icon: "error",
        title: "Error",
        text: "Username and Password are required",
      });
    } else {
      const { data, error } = await useFetch(
        `${config.apiServer}/api/user/signIn`,
        {
          method: "POST",
          body: {
            username: username.value,
            password: password.value,
          },
          server: false,
        }
      );

      if (error.value) {
        Swal.fire({
          icon: "error",
          title: "Error",
          text: "Invalid Username and Password",
        });
      } else {
        console.log(data.value);
      }
    }
  } catch (error) {
    Swal.fire({
      icon: "error",
      title: "Error",
      text: error.message,
    });
  }
};
</script>
