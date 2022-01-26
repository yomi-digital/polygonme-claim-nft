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
        All India Chess League <span class="highlights">3.0</span><br />
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
            <p>
              <em>If you don't have Metamask follow this </em>
              <a href="" target="_blank"><em>guide</em></a>
            </p>
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
              <button class="btn mt-4" type="submit" @click="nextStep()">
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
              <button class="btn mt-4" type="submit" @click="nextStep()">
                Confirm Code
              </button>
            </div>
          </form>
        </div>
        <div v-if="step === 3">
          <button class="btn mt-4" type="submit" @click="conncet()">
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
import Web3 from "web3";
import Web3Modal from "web3modal";
import WalletConnectProvider from "@walletconnect/web3-provider";

import Header from "@/components/Header.vue";

export default {
  name: "Home",
  components: {
    Header,
  },
  data() {
    return {
      step: 1,
      account: "",
      web3: "",
      networks: {
        ethereum: 1,
        rinkeby: 4,
        polygon: 137,
        mumbai: 80001,
        ganache: 5777,
      },
      abi: [
        {
          inputs: [
            {
              internalType: "address",
              name: "_to",
              type: "address",
            },
            {
              internalType: "uint256",
              name: "amount",
              type: "uint256",
            },
            {
              internalType: "string",
              name: "plan",
              type: "string",
            },
          ],
          name: "mint",
          outputs: [],
          stateMutability: "payable",
          type: "function",
        },
      ],
      network: "rinkeby",
      contract: "0xAF42B5dC41F736a167A5B27e3Fb953b89627032b",
      explorerUrl: "https://etherscan.io/tx/",
    };
  },
  methods: {
    nextStep() {
      const app = this;
      app.step = app.step + 1;
    },
    async connect() {
      const app = this;
      const providerOptions = {
        walletconnect: {
          package: WalletConnectProvider,
          options: {
            infuraId: "insert_infura",
          },
        },
      };
      // Instantiating Web3Modal
      const web3Modal = new Web3Modal({
        cacheProvider: true,
        providerOptions: providerOptions,
      });
      const provider = await web3Modal.connect();
      app.web3 = await new Web3(provider);
      // Checking if networkId matches
      const netId = await app.web3.eth.net.getId();
      if (netId !== 1) {
        alert("Switch to Ethereum Network!");
      } else {
        const accounts = await app.web3.eth.getAccounts();
        if (accounts.length > 0) {
          app.balance = await app.web3.eth.getBalance(accounts[0]);
          app.account = accounts[0];
          app.balance = parseFloat(
            app.web3.utils.fromWei(app.balance, "ether")
          ).toFixed(10);
        }
      }
    },
  },
};
</script>
