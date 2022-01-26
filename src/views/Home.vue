<template>
  <div class="home">
    <Header />
    <div class="container">
      <img
        class="my-5 mx-0"
        src="../assets/img/polygon.svg"
        width="20%"
        alt=""
      />
      <h1 class="mt-5">
        All India Chess League 3.0 <br />
        Claim your Badge
      </h1>
    </div>
    <section class="main-section p-5">
      <div class="container">
        <div class="row justify-content-center">
          <div class="mb-5 col-12 col-md-6 col-lg-6">
            <h3>
              This tool was developed for the nft claim you received.<br />
              All you have to do is enter your email, enter the code you
              received via email and sign the transaction with Metamask.
            </h3>
            <p>If you don't have Metamask follow this <a href="">guide</a></p>
          </div>
        </div>
        <div v-if="step === 1">
          <form class="row needs-validation justify-content-center" required>
            <div class="col-md-4 position-relative">
              <label for="validationTooltipUsername" class="form-label"
                >Insert your e-mail</label
              >
              <div class="input-group has-validation">
                <span
                  class="input-group-text"
                  id="validationTooltipUsernamePrepend"
                  >@</span
                >
                <input
                  type="text"
                  class="form-control"
                  id="validationTooltipUsername"
                  aria-describedby="validationTooltipUsernamePrepend"
                  required
                />
                <div class="invalid-tooltip">Please insert a valid e-mail.</div>
              </div>
            </div>
            <div class="col-12">
              <button class="btn mt-4" type="submit" @click="nextStep">
                Request Code
              </button>
            </div>
          </form>
        </div>
        <div v-if="step === 2">
          <form class="row needs-validation justify-content-center" required>
            <div class="col-md-4 position-relative">
              <label for="validationTooltip05" class="form-label"
                >Insert the code that you received via mail</label
              >
              <input
                type="text"
                class="form-control"
                id="validationTooltip05"
                required
              />
              <div class="invalid-tooltip">Please provide a valid code.</div>
            </div>
            <div class="col-12">
              <button class="btn mt-4" type="submit" @click="nextStep">
                Confirm Code
              </button>
            </div>
          </form>
        </div>
        <div v-if="step === 3">
          <button class="btn mt-4" type="submit" @click="nextStep">
            <img src="../assets/img/metamask.png" width="30" alt="" /> Connect
            with Metamask
          </button>
        </div>
      </div>
    </section>
    <div class="img-bg-container">
      <div class="gradient-top"></div>
      <div class="bottom-img"></div>
    </div>
  </div>
</template>

<script>
// @ is an alias to /src
import Header from "@/components/Header.vue";

export default {
  name: "Home",
  components: {
    Header,
  },
  data() {
    return {
      step: 1,
    };
  },
  methods: {
    nextStep() {
      const app = this;
      app.step = app.step + 1;
    },
  },
};
</script>
