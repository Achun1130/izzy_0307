<template>
  <div class="col-md-9">
    <a href="#" class="u-underline" @click="goBack">
      <i class="fas fa-reply mr-2"></i>返回前頁
    </a>
    <main class="row my-3" v-if="product.title">
      <div class="col-sm-6 mb-3 mb-sm-0">
        <div class="w-100 u-bg-cover position-relative h-sm-100"
        style="height: 200px"
        :style="{ 'background-image': 'url(' + product.imageUrl + ')' }">
          <img src="@/assets/images/onsale_icon.png" alt="sale image"
            class="c-product__card__sale" v-if="product.origin_price !== product.price">
        </div>
      </div>
      <div class="col-sm-6">
        <h3 class="h4 font-weight-bold mb-3">
          {{ product.title }}
        </h3>
        <p class="mb-2">
          <span v-if="product.category[0] === '精選豆單'">風味筆記：</span>
          {{ product.description }}
        </p>
        <div class="mb-3">
          <span v-for="(item, key) in product.category" :key="key"
            class="badge badge-pill badge-info"
            :class="{ 'ml-2': key !== 0 }">
            <i class="fas fa-tag mr-1"></i>{{ item }}
          </span>
        </div>
        <ul class="u-border-left-title pl-2 u-fz-sm list-unstyled">
          <li class="mb-1">全店，單筆滿千免運費 (限台灣本島、離島)</li>
          <li>滿額 NT$2,000 即送精美小禮</li>
        </ul>
        <div class="mb-2">售價</div>
        <b class="h5 text-danger font-weight-bold mr-2">
          NT{{ product.price | currency }}
        </b>
        <del v-if="product.origin_price !== product.price" class="text-muted">
          NT{{ product.origin_price | currency }}
        </del>
        <validation-observer v-slot="{ handleSubmit }">
          <form class="row mt-2 no-gutters"
          @submit.prevent="handleSubmit(addCart($event))">
            <div class="col-12">
              <label for="qty" class="mt-2">數量</label>
            </div>
            <div class="col-12 col-sm-6">
              <qty-button :data="data" @getQty="getQty" :qtyClass="['pr-sm-1']"></qty-button>
            </div>
            <div class="w-100 mb-2"></div>
            <div class="col-12">
              <div class="d-flex position-sm-static fixed-bottom p-sm-0 p-2
                shadow-sm-none u-shadow-top"
                style="z-index: 1029;"
                :style="{ 'background-image':
                'url(' + require('@/assets/images/vintage-concrete.png') + ')' }">
                <button type="submit" class="btn btn-outline-info btn-block text-dark mr-2">
                  加入購物車
                </button>
                <button type="submit" class="btn btn-info btn-block mt-0">
                  立即購買
                </button>
              </div>
            </div>
          </form>
        </validation-observer>
      </div>
      <div class="col-12 mt-4 mb-3">
        <ul class="d-flex text-center py-2 list-unstyled mb-0">
          <li class="w-50 mr-1">
            <a href="#" class="shadow btn btn-block btn-secondary rounded-0 py-2 text-center"
              @click.prevent="isPayment = false">商品描述</a>
          </li>
          <li class="w-50">
            <a href="#" class="shadow btn btn-block btn-secondary rounded-0 py-2 text-center"
              @click.prevent="isPayment = true">運送及付款方式</a>
          </li>
        </ul>
      </div>
      <div class="col-12">
        <h4 class="u-border-left-title font-weight-bold pl-3 mb-3 h5-left">
          {{ !isPayment ? '商品描述' : '運送及付款方式' }}
        </h4>
        <div class="text-center"
          v-if="product.category[0] === '精選豆單' && !isPayment">
          <img class="img-fluid u-fadeIn" src="@/assets/images/beans-01.jpg" alt="beans-01">
          <img class="img-fluid u-fadeIn" src="@/assets/images/beans-02.jpg" alt="beans-02">
          <img class="img-fluid u-fadeIn" src="@/assets/images/beans-03.jpg" alt="beans-03">
        </div>
        <div v-if="isPayment"
          class="text-muted d-flex justify-content-lg-between flex-lg-row flex-column u-fadeIn">
          <section>
            <h5>送貨方式</h5>
            <ul class="u-fz-sm">
              <li>7-11取貨付款</li>
              <li>7-11取貨付款</li>
              <li>7-11純取貨不付款</li>
              <li>新竹物流宅配</li>
              <li>順豐國際快遞</li>
            </ul>
          </section>
          <section>
            <h5>付款方式</h5>
            <ul class="u-fz-sm">
              <li>信用卡（支援Visa, Master, JCB）</li>
              <li>超商代碼（需持代碼至超商印出帳單繳費）</li>
              <li>7-11 取貨付款</li>
              <li>ATM 虛擬代碼繳費（需持代碼至實體ATM或網路銀行繳費）</li>
            </ul>
          </section>
        </div>
      </div>
    </main>
  </div>
</template>
<script>
import { mapGetters } from 'vuex';

import QtyButton from '@/components/QtyButton.vue';

export default {
  name: 'ProductSingle',
  data() {
    return {
      data: {
        qty: 1,
      },
      isPayment: false,
    };
  },
  watch: {
    '$route.params': {
      handler() {
        this.$store.dispatch('productModules/getProduct', {
          id: this.$route.params.id,
        });
        this.data.qty = 1;
      },
      deep: true,
      immediate: true,
    },
  },
  methods: {
    goBack() {
      this.$router.back();
    },
    addCart(e) {
      const which = e.submitter.innerText === '加入購物車' ? 'cartLoading' : 'buyLoading';
      if (which === 'buyLoading') {
        this.$router.push('/checkout');
      } else {
        this.$store.dispatch('alertModules/updateMessage',
          { message: '加入購物車', status: 'success' });
        this.$store.commit('cartModules/TOGGLECART');
      }
      this.$store.dispatch('cartModules/addCart', {
        qty: this.data.qty,
        cartProduct: this.product,
      });
    },
    getQty(qty) {
      this.data.qty = qty;
    },
  },
  computed: {
    ...mapGetters('productModules', ['product']),
  },
  components: {
    QtyButton,
  },
};
</script>
