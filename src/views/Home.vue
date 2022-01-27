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
          <div class="row needs-validation justify-content-center" required>
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
                  v-model="email"
                  required
                />
                <div class="invalid-tooltip">Please insert a valid e-mail.</div>
              </div>
            </div>
            <div class="col-12">
              <button
                v-if="!isRequesting"
                class="btn mt-4"
                @click="checkUserNft()"
              >
                Request Code
              </button>
              <div class="mt-4" v-if="isRequesting">
                <p>Requesting code please wait...</p>
                <div class="spinner mt-1">
                  <i class="fas fa-spinner fa-pulse"></i>
                </div>
              </div>
            </div>
          </div>
        </div>
        <div v-if="step === 2">
          <div class="row needs-validation justify-content-center" required>
            <div class="col-md-4 position-relative">
              <label for="validationTooltip05" class="form-label"
                >Insert the code that you have received via mail</label
              >
              <input
                type="text"
                class="form-control"
                id="validationTooltip05"
                required
                v-model="code"
              />
            </div>
            <div v-if="!account">
              <button
                v-if="!account"
                class="btn mt-4"
                @click="connectMetamask()"
              >
                Sign Message
              </button>
            </div>
            <div v-if="account">
              <p>Claiming your nft, please wait...</p>
              <div class="spinner mt-1">
                <i class="fas fa-spinner fa-pulse"></i>
              </div>
            </div>
          </div>
        </div>
        <div v-if="step === 3">
          <h3>
            Congratulation, you will receive your NFT soon! <br />
            Go to
            <a href="https://opensea.io/" target="_blank">opensea.io</a> and
            check in "hidden" section of your account page.
          </h3>
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
import axios from "axios";
import Header from "@/components/Header.vue";

export default {
  name: "Home",
  components: {
    Header,
  },
  data() {
    return {
      step: 1,
      email: "",
      claim: "",
      code: "",
      account: "",
      signature: "",
      existingCode: "",
      isRequesting: false,
      web3: "",
      waitingForCode: "",
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
    async connectMetamask() {
      const app = this;
      if (app.code.length > 0) {
        app.isRequesting = false;
        const providerOptions = {
          walletconnect: {
            package: WalletConnectProvider,
            options: {
              infuraId: "57d9ea9ca92a4449933c2b7d7145187d",
            },
          },
        };
        // Instantiating Web3Modal
        const web3Modal = new Web3Modal({
          cacheProvider: true,
          providerOptions: providerOptions,
        });
        const provider = await web3Modal.connect();
        console.log("this is web3:", web3Modal);
        app.web3 = await new Web3(provider);
        // Checking if networkId matches
        const netId = await app.web3.eth.net.getId();
        if (netId !== 1) {
          alert("Switch to Ethereum Network!");
        } else {
          const accounts = await app.web3.eth.getAccounts();
          if (accounts.length > 0) {
            app.account = accounts[0];
            console.log("account is:", app.account);
            try {
              app.signature = await app.web3.eth.personal.sign(
                app.code,
                app.account
              );

              console.log("signature is:", app.signature);
              app.claimNFT();
            } catch (e) {
              alert(e.message);
            }
          }
        }
      }
    },
    async checkUserNft() {
      const app = this;
      console.log(app.email, app.isRequesting);
      if (app.email.length > 0 && !app.isRequesting) {
        app.isRequesting = true;
        console.log("Init request");
        try {
          let claimableNfts = await axios.get(
            process.env.VUE_APP_API_URL + "/claimable/" + app.email
          );
          console.log("Response is: ", claimableNfts.data[0]);
          if (
            claimableNfts.data[0] !== undefined &&
            claimableNfts.data[0].claim !== undefined &&
            claimableNfts.data[0].claim.length > 0
          ) {
            app.claim = claimableNfts.data[0].claim;
            app.askRedeemCode();
          } else {
            alert("Nothing to claim, sorry!");
            app.isRequesting = false;
          }
        } catch (e) {
          console.log("You don't have any nft to claim!");
          app.isRequesting = false;
        }
      }
    },
    async askRedeemCode() {
      console.log("init code request");
      const app = this;
      if (
        app.isRequesting === true &&
        app.claim !== undefined &&
        app.claim.length > 0
      ) {
        console.log("claim event:", app.claim);
        try {
          let askRedeemCode = await axios.get(
            process.env.VUE_APP_API_URL + "/ask/event/" + app.claim
          );
          console.log("I want code for redeem:", askRedeemCode.data);
          console.log("message is:", askRedeemCode.data.message);
          if (
            askRedeemCode.data.message !== undefined &&
            askRedeemCode.data.message === "Please verify your e-mail now."
          ) {
            app.step = 2;
            app.existingCode = true;
            console.log("I am on step", app.step);
          } else {
            alert("Request Failed, please retry");
            app.isRequesting = false;
          }
        } catch (e) {
          console.log(e);
          alert("Code request failed");
          app.isRequesting = false;
        }
      }
    },
    async claimNFT() {
      const app = this;
      console.log(app.account, app.isRequesting, app.signature);
      if (
        app.account !== undefined &&
        app.isRequesting === false &&
        app.signature.length > 0
      ) {
        try {
          app.isRequesting = true;
          console.log("now isRequesting is:", app.isRequesting);
          let checkCode = await axios.post(
            process.env.VUE_APP_API_URL + "/claim/" + app.claim,
            { address: app.account, secret: app.code, signature: app.signature }
          );
          setTimeout(
            axios.get(process.env.VUE_APP_API_URL + "/run-daemon"),
            200
          );
          console.log("I'm signing", checkCode.data);
          app.isRequesting = false;
          console.log("after checking code isRequesting is", app.isRequesting);
          app.step = 3;
        } catch (e) {
          console.log(e);
          alert("Your code is invalid, check and retry!");
          app.isRequesting = false;
        }
      }
    },
  },
};
</script>
