<template>
  <div class="myCheckoutBox">
    <div class="paypalBox">
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

      <div v-if="paidFor">
        <h1>Payment succeded!</h1>
      </div>

      <div
        id="paypal-button-container"
        class="payPalPayments"
        ref="paypal"
        :class="{ hide: !pay }"
      ></div>
      <div class="">
        <i class="iconCard fab fa-cc-visa"></i
        ><i class="iconCard fab fa-cc-mastercard"></i>
        <i class="iconCard fab fa-cc-paypal"></i>
        <i class="iconCard fab fa-cc-amex"></i>
      </div>
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
        price: 5,
        description: "leg lamp from that one movie",
        img: "",
      },
    };
  },
  created() {
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