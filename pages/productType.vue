<script setup>
import config from "~/config";
import Swal from "sweetalert2";

const showModal = ref(false);

definePageMeta({
  layout: "admin",
});

const name = ref("");
const remark = ref("");
const id = ref("");
const productTypes = ref([]);

const closeModal = () => {
  showModal.value = false;
};

onMounted(async () => {
  await fetchData();
});

const fetchData = async () => {
  try {
    const { data, error } = await useFetch(
      `${config.apiServer}/api/productType/list`,
      {
        method: "GET",
      }
    );
    if (error.value) {
      return Swal.fire({
        icon: "error",
        title: "Save Failed",
        text: "Please try again.",
      });
    } else {
      productTypes.value = data.value?.results || [];
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
    const payload = {
      name: name.value,
      remark: remark.value,
    };

    let url = "";
    let method = "";

    if (id.value === "") {
      url = `${config.apiServer}/api/productType/create`;
      method = "POST";
    } else {
      url = `${config.apiServer}/api/productType/update/${id.value}`;
      method = "PUT";
    }

    const { data, error } = await useFetch(url, {
      method,
      body: payload,
      server: false,
    });

    if (error.value) {
      return Swal.fire({
        icon: "error",
        title: "Save Failed",
        text: error.value?.message || "Please try again.",
      });
    }

    id.value = "";
    await fetchData();
    closeModal();
  } catch (error) {
    Swal.fire({
      icon: "error",
      title: "Error",
      text: error.message,
    });
  }
};

const update = (productTypes) => {
  id.value = productTypes.id;
  name.value = productTypes.name;
  remark.value = productTypes.remark;
  showModal.value = true;
};

const remove = async (id) => {
  try {
    const button = await Swal.fire({
      icon: "warning",
      title: "ยืนยันการลบ",
      text: "คุณต้องการลบประเภทสินค้านี้หรือไหม?",
      showCancelButton: true,
      showConfirmButton: true,
    });

    if (button.isConfirmed) {
      await useFetch(`${config.apiServer}/api/productType/remove/${id}`, {
        method: "DELETE",
        server: false,
      });
      await fetchData();
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

<template>
  <div class="title">ประเภทสินค้า</div>
  <div class="p-4">
    <button class="btn btn-primary cursor-pointer" @click="showModal = true">
      <i class="fa fa-plus"></i>
      เพิ่มประเภทสินค้า
    </button>

    <table class="table table-bordered mt-3">
      <thead>
        <tr>
          <th class="text-left">ชื่อ</th>
          <th>หมายเหตุ</th>
          <th width="110px"></th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="productType in productTypes" :key="productType.id">
          <td>{{ productType.name }}</td>
          <td>{{ productType.remark }}</td>
          <td class="text-center">
            <button class="btn btn-primary me-1" @click="update(productType)">
              <i class="fa fa-pencil"></i>
            </button>
            <button class="btn btn-danger" @click="remove(productType.id)">
              <i class="fa fa-trash"></i>
            </button>
          </td>
        </tr>
      </tbody>
    </table>
  </div>

  <Modal v-if="showModal" title="เพิ่มประเภทสินค้า" @close="closeModal">
    <div>ชื่อ</div>
    <input type="text" class="form-control" v-model="name" />
    <div class="mt-3">หมายเหตุ</div>
    <input type="text" class="form-control" v-model="remark" />

    <button class="btn btn-primary mt-3 cursor-pointer" @click="save">
      <i class="fa fa-check me-2"></i>
      บันทึก
    </button>
  </Modal>
</template>
