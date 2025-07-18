<script setup>
import Swal from "sweetalert2";
import config from "~/config";

definePageMeta({
  layout: "admin",
});

const showModal = ref(false);
const materials = ref([]);
const id = ref("");
const name = ref("");
const unit = ref("");
const price = ref(0);
const remark = ref("");

const showModalStockMaterial = ref(false);
const stockMaterialMaterialId = ref("");
const listMaterials = ref([]);
const stockMaterialQuantity = ref(0);
const stockMaterialPrice = ref(0);
const stockMaterialRemark = ref("");
const stockMaterialId = ref("");

onMounted(async () => {
  await fetchData();
});

const openModalStockMaterial = async (material) => {
  showModalStockMaterial.value = true;

  try {
    const { data, error } = await useFetch(
      `${config.apiServer}/api/material/list`,
      {
        method: "GET",
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
    listMaterials.value = data.value?.results || [];
    stockMaterialMaterialId.value = materials.value[0].id;
  } catch (error) {
    Swal.fire({
      icon: "error",
      title: "error",
      text: error.message,
    });
  }
};

const closeModalStockMaterial = () => {
  showModalStockMaterial.value = false;
  stockMaterialQuantity.value = 0;
  stockMaterialPrice.value = 0;
  stockMaterialRemark.value = "";
  stockMaterialId.value = "";
};

const closeModal = () => {
  showModal.value = false;
  name.value = "";
  unit.value = "";
  price.value = 0;
  remark.value = "";
};

const fetchData = async () => {
  try {
    const { data, error } = await useFetch(
      `${config.apiServer}/api/material/list`,
      {
        method: "GET",
        server: false,
      }
    );

    for (const material of data.value.results) {
      material.balance = 0;

      for (const stockMaterial of material.StockMaterial) {
        material.balance += stockMaterial.quantity;
      }
    }

    if (error.value) {
      return Swal.fire({
        icon: "error",
        title: "Error",
        text: error.value.message || "ไม่สามารถโหลดข้อมูลได้",
      });
    }
    materials.value = data.value?.results || [];
  } catch (error) {
    Swal.fire({
      icon: "error",
      title: "error",
      text: error.message,
    });
  }
};

const save = async () => {
  try {
    const payload = {
      name: name.value,
      unit: unit.value,
      price: price.value,
      remark: remark.value,
    };
    let url = "";
    let method = "";
    if (id.value === "") {
      url = `${config.apiServer}/api/material/create`;
      method = "POST";
    } else {
      url = `${config.apiServer}/api/material/update/${id.value}`;
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

const edit = (material) => {
  id.value = material.id;
  name.value = material.name;
  unit.value = material.unit;
  price.value = material.price;
  remark.value = material.remark;

  showModal.value = true;
};

const remove = async (id) => {
  try {
    const button = await Swal.fire({
      icon: "warning",
      title: "ยืนยันการลบ",
      text: "คุณต้องการลบวัสดุ, ส่วนผสมนี้หรือไม่",
      showCancelButton: true,
      showConfirmButton: true,
    });

    if (button.isConfirmed) {
      await useFetch(`${config.apiServer}/api/material/remove/${id}`, {
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

const saveStockMaterial = async () => {
  try {
    const payload = {
      materialId: stockMaterialMaterialId.value,
      quantity: stockMaterialQuantity.value,
      price: stockMaterialPrice.value,
      remark: stockMaterialRemark.value,
    };
    const { data, error } = await useFetch(
      `${config.apiServer}/api/stockMaterial/create`,
      {
        method: "POST",
        body: payload,
        server: false,
      }
    );

    if (error.value) {
      return Swal.fire({
        icon: "error",
        title: "Save Failed",
        text: error.value?.message || "Please try again.",
      });
    }

    closeModalStockMaterial();
    fetchData();
  } catch (error) {
    Swal.fire({
      icon: "error",
      title: "Error",
      text: error.message,
    });
  }
};

const selectedStockMaterial = () => {
  const material = listMaterials.value.find(
    (m) => m.id === stockMaterialMaterialId.value
  );
  stockMaterialPrice.value = material.price;
  stockMaterialId.value = material.id;
};
</script>

<template>
  <div class="title">วัสดุ, ส่วนผสม</div>
  <div class="p-4">
    <button class="btn me-1" @click="showModal = true">
      <i class="fa fa-plus mr-1"></i>
      เพิ่มวัสดุ, ส่วนผสม
    </button>
    <button class="btn me-1" @click="openModalStockMaterial">
      <i class="fa fa-arrow-circle-down mr-1"></i>
      รับเข้าสต๊อก
    </button>
    <button class="btn">
      <i class="fa fa-history mr-1"></i>
      ประวัติการรับเข้าสต๊อก
    </button>

    <table class="table mt-3">
      <thead>
        <tr>
          <th class="text-left">ชื่อ</th>
          <th class="text-left">หมายเหตุ</th>
          <th class="text-right">คงเหลือ</th>
          <th class="text-left">หน่วย</th>
          <th class="text-right">ราคา</th>
          <th>วันที่รับเข้าล่าสุด</th>
          <th width="110px"></th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="material in materials" :key="material.id">
          <td>{{ material.name }}</td>
          <td>{{ material.remark }}</td>
          <td class="text-right">{{ material.balance }}</td>
          <td>{{ material.unit }}</td>
          <td class="text-right">
            {{ material.price.toLocaleString("th-TH") }}
          </td>
          <td>15/07/2568 15.00</td>
          <td class="text-center">
            <button class="btn btn-primary me-1" @click="edit(material)">
              <i class="fa fa-pencil"></i>
            </button>
            <button class="btn btn-danger" @click="remove(material.id)">
              <i class="fa fa-times"></i>
            </button>
          </td>
        </tr>
      </tbody>
    </table>
  </div>

  <Modal v-if="showModal" title="วัสดุ, ส่วนผสม" @close="closeModal">
    <div>ชื่อ</div>
    <input type="text" v-model="name" class="form-control" />
    <div class="mt-3">หน่วยเรียกว่า</div>
    <input type="text" v-model="unit" class="form-control" />
    <div class="mt-3">ราคา</div>
    <input type="text" v-model="price" class="form-control" />
    <div class="mt-3">หมายเหตุ</div>
    <input type="text" v-model="remark" class="form-control" />
    <button class="btn mt-3" @click="save">
      <i class="fa fa-save mr-1"></i>
      บันทึก
    </button>
  </Modal>

  <Modal
    v-if="showModalStockMaterial"
    title="รับเข้าสต๊อก"
    @close="closeModalStockMaterial"
  >
    <div>วัสดุ, ส่วนผสม</div>
    <select
      v-model="stockMaterialMaterialId"
      class="form-control"
      @change="selectedStockMaterial"
    >
      <option
        v-for="material in materials"
        :key="material.id"
        :value="material.id"
      >
        {{ material.name }}
      </option>
    </select>
    <div class="mt-3">จำนวน</div>
    <input type="number" v-model="stockMaterialQuantity" class="form-control" />

    <div class="mt-3">ราคา</div>
    <input type="number" v-model="stockMaterialPrice" class="form-control" />

    <div class="mt-3">หมายเหตุ</div>
    <input type="text" v-model="stockMaterialRemark" class="form-control" />

    <button class="btn mt-3" @click="saveStockMaterial">
      <i class="fa fa-check mr-1"></i> บันทึก
    </button>
  </Modal>
</template>
