<script setup>
import { ref } from 'vue';
import AppMenuItem from './AppMenuItem.vue';
import { useLayout } from './composables/layout';

const { menuModel } = useLayout();
</script>

<template>
    <ul class="layout-menu">
        <template v-for="(item, i) in menuModel" :key="item">
            <app-menu-item v-if="!item.separator" :item="item" :index="i"></app-menu-item>
            <li v-if="item.separator" class="menu-separator"></li>
        </template>
    </ul>
</template>

<style lang="scss" scoped></style>
