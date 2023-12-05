<template>
    <!-- <button @click="toggleMenu()" class="p-link layout-topbar-button">
        <i class="pi pi-user"></i>
        <span>Profile</span>
    </button> -->
    <!-- <div class="p-link layout-topbar-button">
        <i class="pi pi-user" @click.stop="toggleMenu"></i>
    </div>
      <Menu :class="{'show':show}" :model="menuitems" /> -->

    <Menu ref="menu" :model="menuitems" :popup="true" />
      <div class="p-link layout-topbar-button">
        <i class="pi pi-user" @click.stop="toggleMenu"></i>
    </div>
    <!-- <Button type="button" label="Options" icon="pi pi-angle-down" @click="toggleMenu" style="width: auto" /> -->
  </template>
  
  <script setup>
  import { ref } from 'vue';

    const menu = ref(null);
    const contextMenu = ref(null);
    const toggleMenu = (event) => {
        menu.value.toggle(event);
    };


  const menuitems = ref([        
        {
            label: 'Log Out',
            icon: 'pi pi-fw pi-plus',
            route: '/auth/login'
        },
        {
            label: 'Edit',
            icon: 'pi pi-fw pi-user-edit'
        }
    ]);

  </script>
  
  <style scoped>
  .menu-container {
    position: relative;
  }
  
  .menu {
    position: absolute;
    top: 100%;
    left: 0;
    display: none;
    /* Additional styling for your menu */
  }

  .show {
  display: block !important;
}
  </style>