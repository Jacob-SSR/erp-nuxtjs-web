<script setup>
definePageMeta({
  layout: "admin",
});

const showModal = ref(false);

const materials = ref([
  {
    id: 1,
    name: "asd",
    remark: "asda",
    balance: 50,
    unit: "sss",
    price: 500,
  },
  {
    id: 2,
    name: "aasd",
    remark: "sasda",
    balance: 500,
    unit: "sssdd",
    price: 50,
  },
  {
    id: 3,
    name: "affsd",
    remark: "affffsda",
    balance: 5550,
    unit: "ssasds",
    price: 50220,
  },
]);
</script>

<template>
  <div class="title">วัสดุ, ส่วนผสม</div>
  <div class="p-4">
    <button class="btn me-1" @click="showModal = true">
      <i class="fa fa-plus mr-1"></i>
      เพิ่มวัสดุ, ส่วนผสม
    </button>
    <button class="btn me-1">
      <i class="fa fa-list mr-1"></i>
      รายการวัสดุ, ส่วนผสม
    </button>
    <button class="btn me-1">
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
          <td class="text-right">{{ material.price }}</td>
          <td>15/07/2568 15.00</td>
          <td class="text-center">
            <button class="btn btn-primary me-1">
              <i class="fa fa-pencil"></i>
            </button>
            <button class="btn btn-danger">
              <i class="fa fa-times"></i>
            </button>
          </td>
        </tr>
      </tbody>
    </table>
  </div>
</template>
