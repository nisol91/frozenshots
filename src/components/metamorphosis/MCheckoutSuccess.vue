<template>
  <div class="checkoutSuccessBox">
    <div class="cSBtn">checkout success: check your email</div>
  </div>
</template>
<script>
export default {
  created() {
    this.$store.commit("toggleHomePage", false);
    setTimeout(() => {
      this.$store.commit("toggleHomeMenuColor", true);
    }, 2000);
  },
};
</script>
<style lang="scss">
.checkoutSuccessBox {
  width: 100%;
  height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;
  .cSBtn {
    display: flex;
    justify-content: center;
    align-items: center;
    font-size: 35px;
    border: 2px solid black;
    border-radius: 50px;
    padding: 10px;
  }
}
</style>