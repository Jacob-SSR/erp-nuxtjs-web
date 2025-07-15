<script setup>
import Swal from "sweetalert2";
import config from "~/config";

definePageMeta({
  layout: "admin",
});

const name = ref("");
const username = ref("");
const password = ref("");
const confirmPassword = ref("");
const level = ref("admin");

onMounted(() => {
  fetchData();
});

const fetchData = async () => {
  try {
    const token = localStorage.getItem(config.token);
    const { data, error } = await useFetch(
      `${config.apiServer}/api/user/info`,
      {
        method: "GET",
        headers: {
          Authorization: `Bearer ${token}`,
        },
        server: false,
      }
    );

    if (error.value) {
      return Swal.fire({
        icon: "error",
        title: "Error",
        text: error.value.message || "ไม่สามารถโหลดข้อมูลได้",
      });
    }
    if (data.value?.result) {
      name.value = data.value.result.name;
      username.value = data.value.result.username;
      level.value = data.value.result.level;
    }
  } catch (error) {
    Swal.fire({
      icon: "error",
      title: "Error",
      text: error.message,
    });
  }
};

const save = async () => {
  try {
    if (password.value !== confirmPassword.value) {
      return Swal.fire({
        icon: "error",
        title: "รหัสผ่านไม่ตรงกัน",
        text: "กรุณายืนยันรหัสผ่านให้ตรงกัน",
      });
    }
    const token = localStorage.getItem(config.token);
    const payload = {
      name: name.value,
      username: username.value,
      password: password.value,
      level: level.value,
    };
    const { error } = await useFetch(`${config.apiServer}/api/user/update`, {
      method: "PUT",
      body: payload,
      headers: {
        Authorization: `Bearer ${token}`,
      },
      server: false,
    });
    if (error.value) {
      return Swal.fire({
        icon: "error",
        title: "Error",
        text: error.value.message || "ไม่สามารถบันทึกข้อมูลได้",
      });
    }
    Swal.fire({
      icon: "success",
      title: "บันทึกข้อมูล",
      text: "บันทึกข้อมูลสำเร็จ",
      timer: 1000,
    });
  } catch (error) {
    Swal.fire({
      icon: "error",
      title: "Error",
      text: error.message,
    });
  }
};
</script>

<template>
  <div class="title">ข้อมูลผู้ส่วนตัว</div>
  <div class="p-4">
    <div class="">
      <div class="">
        <div class="form-group">
          <div>ชื่อ</div>
          <input type="text" class="form-control" v-model="name" />

          <div class="mt-3">username</div>
          <input type="text" class="form-control" v-model="username" />

          <div class="mt-3">
            รหัสผ่าน
            <span class="text-red">(ถ้าต้องการเปลี่ยน ให้กรอกข้อมูล)</span>
          </div>
          <input type="password" class="form-control" v-model="password" />

          <div class="mt-3">
            ยืนยันรหัสผ่าน
            <span class="text-red">(ถ้าต้องการเปลี่ยน ให้กรอกข้อมูล)</span>
          </div>
          <input
            type="password"
            class="form-control"
            v-model="confirmPassword"
          />

          <div class="mt-3">สิทธิ์การเข้าสู่ระบบ</div>
          <input
            type="radio"
            id="admin"
            v-model="level"
            value="admin"
            class="me-1"
          />
          <label for="admin" class="me-2">Admin</label>
          <input
            type="radio"
            id="user"
            v-model="level"
            value="user"
            class="me-1"
          />
          <label for="user">User</label>

          <div class="mt-3">
            <button class="btn btn-primary" @click="save">
              <i class="fa fa-check mr-1"></i>
              บันทึก
            </button>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>
