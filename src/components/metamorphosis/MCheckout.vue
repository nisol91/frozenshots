<template>
  <div class="myCheckoutBox">
    <div class="paypalBox" v-if="!paidFor">
      <form class="mailForm">
        <div class="mailTitle">Insert your email</div>
        <div class="mailSub">we need it to send your download link</div>

        <input
          v-if="!pay"
          id="email"
          type="email"
          class="form-control"
          name="email"
          value
          required
          v-model="email"
        />
        <div v-if="errors.length" class="errorMail">
          <div v-for="error in errors" :key="error + '_error'">{{ error }}</div>
        </div>

        <input
          v-if="pay"
          id="email"
          type="email"
          class="form-control"
          name="email"
          value
          required
          v-model="email"
          disabled
        />
        <v-btn
          color="primary"
          rounded
          dark
          depressed
          class="payBtn"
          @click="checkForm"
        >
          ok, pay
        </v-btn>
      </form>
      <div v-if="!paidFor">
        <h1 class="amountPay">Total amount - ${{ product.price }}</h1>
      </div>

      <div
        id="paypal-button-container"
        class="payPalPayments"
        ref="paypal"
        :class="{ hide: !pay || !product.price }"
      ></div>
      <div class="">
        <i class="iconCard fab fa-cc-visa"></i
        ><i class="iconCard fab fa-cc-mastercard"></i>
        <i class="iconCard fab fa-cc-paypal"></i>
        <i class="iconCard fab fa-cc-amex"></i>
      </div>
    </div>
    <div v-if="paidFor" class="paySuccessBox">
      <h3 class="paySuccessText">
        Payment succeded! A mail was sent to your email adress with the download
        link
      </h3>
      <h6 class="paySuccessText">
        or, if you prefer: click here to download your fotos
      </h6>

      <v-btn
        color="primary"
        rounded
        dark
        depressed
        class="payBtn"
        @click="checkForm"
      >
        download fotos
      </v-btn>
    </div>
  </div>
</template>

<script>
export default {
  name: "HelloWorld",

  data: function () {
    return {
      email: "",
      errors: [],
      pay: false,
      loaded: false,
      paidFor: false,
      product: {
        price: null,
        description: "your fotos",
        img: "",
      },
      orderData: null,
    };
  },
  created() {
    this.product.price = this.$store.state.finalPrice;

    this.$store.commit("toggleHomePage", false);
    setTimeout(() => {
      this.$store.commit("toggleHomeMenuColor", true);
    }, 2000);
  },
  mounted: function () {
    // inizializzo paypal: mi crea un tag script in questo componente
    const script = document.createElement("script");
    script.src =
      "https://www.paypal.com/sdk/js?client-id=AdhATzJ35_m0BhibXyWXQYzRNeWzW75jaYY_woIJpmgE7VrwihbGKtpYSjbT_FkfQwCIDg8NK2yWmCAy&disable-funding=sofort,mybank";
    script.addEventListener("load", this.setLoaded);
    document.body.appendChild(script);
  },
  methods: {
    saveOrderAndProceed() {
      console.log("brev, et paghe");
      // cancella basket - state
      this.$store.dispatch("clearFotoBasket");
      // salva ordine su firestore - state
      this.$store.dispatch("saveFotoOrder", this.orderData);
      // genera url di scaricamento

      // bottone con url di scaricamento

      // invia mail con bottone con url di scaricamento
    },
    checkForm() {
      this.errors = [];
      if (!this.email) {
        this.errors.push("Email required.");
        return;
      } else if (!this.validEmail(this.email)) {
        this.errors.push("Valid email required.");
        return;
      }
      this.pay = true;
    },
    validEmail: function (email) {
      var re = /^(([^<>()[\]\\.,;:\s@"]+(\.[^<>()[\]\\.,;:\s@"]+)*)|(".+"))@((\[[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\])|(([a-zA-Z\-0-9]+\.)+[a-zA-Z]{2,}))$/;
      return re.test(email);
    },
    setLoaded: function () {
      this.loaded = true;
      window.paypal
        .Buttons({
          style: {
            layout: "vertical",
            color: "blue",
            shape: "pill",
            label: "paypal",
          },
          createOrder: (data, actions) => {
            return actions.order.create({
              purchase_units: [
                {
                  description: this.product.description,
                  amount: {
                    currency_code: "USD",
                    value: this.product.price,
                  },
                },
              ],
            });
          },
          onApprove: async (data, actions) => {
            const order = await actions.order.capture();
            this.paidFor = true;
            console.log(order);
            this.orderData = order;
            this.saveOrderAndProceed();
          },
          onError: (err) => {
            console.log(err);
          },
        })
        .render(this.$refs.paypal);
    },
  },
};
</script>
<style lang="scss">
.paySuccessBox {
  width: 100%;
  padding: 3vw;
  display: flex;
  justify-content: center;
  flex-direction: column;
  .paySuccessText {
    text-align: center;
  }
}
.myCheckoutBox {
  width: 100%;
  min-height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;
}
.check {
  width: 100px;
  height: 100px;
}
.paypalBox {
  width: 100%;
  display: flex;
  justify-content: center;
  align-items: center;
  flex-direction: column;
  .payPalPayments {
    width: 100%;
    display: flex;

    justify-content: center;
    align-items: center;
    flex-direction: column;
    padding: 10px;
    margin-bottom: 50px;
  }
}
.amountPay {
  font-size: 20px;
}
.mailTitle {
  font-size: 20px;
  margin: 5px 0;
}
.mailSub {
  font-size: 10px;
  margin: 5px 0;
}
.iconCard {
  font-size: 30px;
  margin: 0 15px;
}
.mailForm {
  width: 40%;
  display: flex;
  justify-content: center;
  align-items: center;
  flex-direction: column;
}
.payBtn {
  margin: 10px 0;
}
.hide {
  display: none;
}
.errorMail {
  color: red;
  font-size: 10px;
  margin: 10px 0;
}
// ##
@media (max-width: 700px) {
  .mailForm {
    width: 90%;
  }
}
</style>