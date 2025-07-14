<script setup></script>

<template>
  <div>
    <div class="sidebar-title">NuxtERP</div>
    <div class="sidebar-avatar">
      <img
        src="https://via.placeholder.com/150"
        alt="avatar"
        class="w-10 h-10 rounded-full mx-auto"
      />
      <div class="text-center text-white text-sm mt-3">Admin System</div>
      <div class="sidebar-menu">
        <NuxtLink class="nav-link" to="/home">
          <i class="fa fa-home"></i>
          Dashborad
        </NuxtLink>
        <NuxtLink class="nav-link" to="/productType">
          <i class="fa fa-file-alt"></i>
          ประเภทสินค้า
        </NuxtLink>
      </div>
    </div>
  </div>
</template>
