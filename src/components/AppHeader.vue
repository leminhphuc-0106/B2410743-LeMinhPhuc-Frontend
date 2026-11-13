
<template>
  <nav class="navbar navbar-expand navbar-dark bg-dark px-3">
    <!-- Tên ứng dụng -->
    <router-link to="/" class="navbar-brand">
      Ứng dụng Quản lý danh bạ
    </router-link>

    <!-- Danh mục điều hướng -->
    <div class="navbar-nav me-auto">
      <router-link
        :to="{ name: 'contactbook' }"
        class="nav-link"
      >
        Danh bạ
        <i class="fas fa-address-book ms-1"></i>
      </router-link>
    </div>
  </nav>
</template>
