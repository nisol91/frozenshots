<template>
  <div
    class="basket fade-in-home"
    :style="{
      height: `${splash ? '100vh' : 'auto'}`,
      justifyContent: `${splash ? 'center' : 'flex-start'}`,
    }"
    v-scroll="scrolled"
  >
    <div v-if="splash" class="canvasBoxBabylon">
      <div class="splashText">FrozenShots</div>
    </div>
    <div class="basketBox" v-if="!splash">
      <div class="bTitle">
        <router-link class="btn nav-button" :to="{ name: 'mFilters' }">
          <i class="fas fa-arrow-left"></i>
        </router-link>
        <div class="basketTitle">basket</div>
      </div>

      <div class="basketProd" v-for="(item, i) in basket" :key="i + 'item'">
        <div class="basketLeft">
          <div class="prodName">
            {{ item.id }}
          </div>
          <!-- <div class="prodDescription">
            {{ item.description }}
          </div> -->
          <v-img
            :src="item.src"
            class="grey lighten-2 prodMedia imgBasket"
            :aspect-ratio="16 / 9"
          >
            <template v-slot:placeholder>
              <v-row class="fill-height ma-0" align="center" justify="center">
                <v-progress-circular
                  indeterminate
                  color="grey lighten-5"
                ></v-progress-circular>
              </v-row>
            </template>
          </v-img>
          <div class="actions">
            <v-tooltip bottom class="">
              <template v-slot:activator="{ on, attrs }">
                <v-icon
                  v-bind="attrs"
                  v-on="on"
                  class="actionIcon"
                  @click="removeFromBasket(item.id)"
                  >mdi-delete</v-icon
                >
              </template>
              <span>remove item</span>
            </v-tooltip>
          </div>
        </div>

        <div class="prodPrice">{{ strandardPrice }}€</div>
      </div>
    </div>
    <div
      class="checkoutBox"
      v-if="!splash"
      :class="{ checkoutBoxScroll: scroll > 20 }"
    >
      <v-btn color="primary" rounded dark depressed @click="clearBasket">
        clear basket
        <v-icon class="actionIcon">mdi-basket-remove</v-icon></v-btn
      >
      <div class="checkTop">
        <span class="prodName">Total items: </span>
        <span class="prodName">{{ this.basket.length }}</span>
      </div>
      <div class="checkTop">
        <span class="prodName">Total price: </span>
        <span class="prodName">{{ totalPrice }} €</span>
      </div>

      <div class="checkoutBtnBox btn nav-btn" @click="processPayments">
        <v-btn color="primary" rounded dark depressed class="checkoutBtn">
          checkout
        </v-btn>
      </div>
    </div>
  </div>
</template>
<script>
import { mapState, mapGetters } from "vuex";

export default {
  data() {
    return {
      basket: [],
      splash: true,
      scroll: null,
      strandardPrice: 5,
    };
  },
  created() {
    this.$store.commit("toggleHomePage", false);
    this.scroll = window.scrollY;
    this.basket = this.$store.state.selectedFotos;
    // console.log(this.basket);
    this.setSplash();
  },
  methods: {
    processPayments() {
      this.$router.push({
        name: "mCheckout",
      });
    },
    scrolled(position) {
      this.scroll = position;
    },
    setSplash() {
      setTimeout(() => {
        this.splash = false;
      }, 3000);
    },

    removeFromBasket(id) {
      this.$store.dispatch("deSelectFoto", id);
    },

    clearBasket() {
      this.$store.dispatch("clearFotoBasket");
      this.basket = this.$store.state.basket.items;
    },
  },
  computed: {
    ...mapState({
      isLoggedIn: "isLoggedIn",
      userRole: "userRole",
      globalMessage: "globalMessage",
    }),
    totalPrice: function () {
      var totalPrice = 0;
      this.basket.forEach((el) => {
        totalPrice += this.strandardPrice * 1;
      });
      return totalPrice;
    },
  },
};
</script>
<style lang="scss">
.basket {
  display: flex;
  justify-content: center;
  padding-top: 100px;
  background-color: #0076ff;
  width: 100%;
}
.basketTitle {
  font-size: 28px;
  margin-left: 50px;
  font-weight: bold;
}
.bTitle {
  display: flex;
}
.basketBox {
  width: 50%;
  padding: 20px;
  min-height: 100vh;
  display: flex;
  flex-direction: column;
}
.basketProd {
  width: 90%;
  display: flex;
  justify-content: space-between;
  border-bottom: 2px solid #474343;
  padding: 10px;
  margin-bottom: 10px;
}
.basketLeft {
  display: flex;
  flex-direction: column;
  justify-content: space-between;
  width: 90%;
}
.checkoutBox {
  position: fixed;
  top: 110px;
  right: 10px;
  width: 45%;
  margin: 10px;
  padding: 30px;
  background: rgba(53, 58, 102, 0.349);
  border-radius: 4px;
  display: flex;
  justify-content: space-between;
  flex-direction: column;
  .checkTop {
    display: flex;
    justify-content: space-between;
    width: 100%;
    margin: 10px 0;
  }
}
.checkoutBtnBox {
  width: 100%;
  .checkoutBtn {
    width: 100%;
  }
}
.imgBasket {
  width: 230px;
}
.actions {
  margin: 20px 0;
  font-size: 20px;
  width: 100%;
  display: flex;
  justify-content: flex-start;
  .actionIcon {
    margin: 0 10px 0 0;
  }
}
// ##
@media (max-width: 800px) {
  .checkoutBox {
    position: fixed;
    height: auto;
    top: 66vh;
    right: 0px;
    width: 100%;
    margin: 0px;
    padding: 10px;
    background: rgba(53, 58, 102, 0.349);
    border-radius: 4px;
    display: flex;
    justify-content: center;
    flex-direction: column;
    transition: 1s;
    .checkTop {
      margin: 3px 0;
    }
  }
  .checkoutBoxScroll {
    transition: 1s;
    top: 68vh;
  }
  .basketBox {
    width: 100%;
    padding: 20px;
    padding-bottom: 250px;
  }
}
</style>