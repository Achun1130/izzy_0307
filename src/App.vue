<template>
  <div id="app">
    <loading :active.sync="isLoading">
      <template slot="default">
        <img src="@/assets/images/loading-unscreen.gif" alt="" width="200" height="200">
      </template>
      <template slot="after">
        <div class="loadingio-spinner-ellipsis-qr5cx9upowh"><div class="ldio-ww3qqn4yenm">
        <div></div><div></div><div></div><div></div><div></div>
        </div></div>
      </template>
    </loading>
    <header>
      <navheader></navheader>
    </header>
    <alert></alert>
    <sidebar-cart></sidebar-cart>
    <scroll-top></scroll-top>
    <router-view class="u-main__height"></router-view>
    <index-footer></index-footer>
    <product-modal></product-modal>
  </div>
</template>

<script>
import { mapGetters, mapActions } from 'vuex';

import Navheader from './components/Navheader.vue';
import Alert from './components/Alert.vue';
import ScrollTop from './components/ScrollTop.vue';
import SidebarCart from './components/SidebarCart.vue';
import IndexFooter from './components/Footer.vue';
import ProductModal from './components/ProductModal.vue';

export default {
  name: 'App',
  methods: {
    ...mapActions('productModules', ['getProducts']),
  },
  computed: {
    ...mapGetters(['isLoading']),
  },
  created() {
    this.getProducts();
  },
  components: {
    Navheader,
    Alert,
    ScrollTop,
    SidebarCart,
    IndexFooter,
    ProductModal,
  },
};
</script>

<style lang="scss">
@import "./assets/scss/all";
</style>
