<template>
    <div :class="{'show':show}" class="header-search">
      <el-select
      ref="headerSearchSelect"
      v-model="search"
      :remote-method="querySearch"
      filterable
      default-first-option
      remote
      placeholder="Search"
      class="header-search-select"
      @change="change"
      >
      <el-option
      v-for="item in options"
      :key="item.item.path"
      :value="item.item"
      :label="item.item.title"
      />
      </el-select>
  </div>
  <div class="p-link layout-topbar-button">
    <i class="pi pi-search" @click.stop="click"></i>
  </div>
</template>

<style lang="scss" scoped>
.header-search {
  display: flex;
  align-items: center;
  font-size: 0 !important;


  // .search-icon {
  //   cursor: pointer;
  //   font-size: 18px;
  //   vertical-align: middle;
  // }

  .header-search-select {
    font-size: 18px;
    transition: width 0.2s;
    width: 0;
    overflow: hidden;
    background: transparent;
    border-radius: 0;
    display: inline-block;
    vertical-align: middle; // Add this line to vertically align with the icon

  }

  &.show {
    .header-search-select {
      width: 210px;
      margin-left: 10px;
    }
  }
}
</style>


<script setup>
import { ref, onMounted, watch, nextTick } from 'vue';
import Fuse from 'fuse.js';
// import path from 'path';
import { useRouter } from 'vue-router';
import { useLayout } from '~/layouts/composables/layout';
const { menuModel } = useLayout();

const router = useRouter();
const search = ref('');
const options = ref([]);
const searchPool = ref([]);
const show = ref(false);
let fuse = null;
const headerSearchSelect = ref(null);

const generateRoutes = (menuItems, basePath = '/', prefixTitle = []) => {
  let res = [];
  for (const menuItem of menuItems) {
    if (menuItem.items || menuItem.length > 1) {
      const tempmenuItem = generateRoutes(menuItem.items);
      // res.push(tempmenuItem);
      res = [...res, ...tempmenuItem]
      continue
    }
    const data = {
      path: menuItem.to,
      title: menuItem.label,
    };
    if (menuItem.to){
      res.push(data);
    }
  }
  return res;
};


const initFuse = (list) => {
  fuse = new Fuse(list, {
    shouldSort: true,
    threshold: 0.4,
    location: 0,
    distance: 100,
    maxPatternLength: 32,
    minMatchCharLength: 1,
    keys: [
      {
        name: 'title',
        weight: 0.7,
      },
      {
        name: 'path',
        weight: 0.3,
      },
    ],
  });
};

onMounted(() => {
  searchPool.value = generateRoutes(menuModel.value);
  initFuse(searchPool.value);
});


// watch(router.getRoutes, () => {
//   searchPool.value = generateRoutes(router.getRoutes());
// });

watch(show, (value) => {
  if (value) {
    document.body.addEventListener('click', close);
  } else {
    document.body.removeEventListener('click', close);
  }
});

const close = () => {
  if (ref.headerSearchSelect) ref.headerSearchSelect.blur();
  options.value = [];
  show.value = false;
};

const click = () => {
  show.value = !show.value;
  console.log('clicked')
  if (show.value) {
    nextTick(() => {
      if (headerSearchSelect.value) headerSearchSelect.value.focus();
    });
  }
};

const change = (val) => {
  router.push(val.path);
  search.value = '';
  options.value = [];
  nextTick(() => {
    show.value = false;
  });
};

const querySearch = (query) => {

  if (query !== '') {
    options.value = fuse.search(query);
  } else {
    options.value = [];
  }
};
</script>
