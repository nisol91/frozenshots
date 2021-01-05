<template>
  <div class="myCheckoutBox">
    <div class="">checkout</div>
  </div>
</template>

<script>
import Stripe from "stripe";
export default {
  data() {
    return {};
  },
  async created() {
    this.$store.commit("toggleHomePage", false);
    setTimeout(() => {
      this.$store.commit("toggleHomeMenuColor", true);
    }, 2000);
  },
  methods: {},
};
</script>

<style >
.myCheckoutBox {
  width: 100%;
  height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;
}
.check {
  width: 100px;
  height: 100px;
}
</style>