<template>
  <div>
    <notifications width='60%' position="top center" group="foo"/>
    <div class="section mt-4">
      <div class="container-flex bg-footer py-5">
        <div class="container-my mx-auto">
          <div class="row d-flex justify-content-center align-items-center mt-3">
            <div class="col-8 col-md-7 col-lg-6 d-flex justify-content-center">
              <button
                class="btn btn-lg btn-my btn-my-shadow text-light w600 px-4 w-100"
                @click="generateWallets"
              >GENERATE SEED/ WALLET PAIR</button>
            </div>
          </div>
        </div>
      </div>
      <div class="row d-flex justify-content-center align-items-center mt-2">
        <img class="icon-2" src="../assets/img/arrow.svg">
      </div>
    </div>
    <div class="section">
      <div class="container-my mx-auto py-3">
        <div class="row d-flex justify-content-center align-middle">
          <div class="col-12">
            <h2 class="w700 text-center text-primary">Generated Wallet</h2>
          </div>
          <div class="col-12">
            <h6 class="text-primary">Private Seed (DO NOT SHARE WITH ANYONE)</h6>
            <div class="input-group">
                <input
                  v-model="generatedWalletseed"
                  class="text-dark h6 text-area p-3 font-secondary w600"
                  id="generatedseed"
                  readonly
                />
                <div class="input-group-append" @click="copyseed">
                  <div class="input-group-text">
                    <svg class="icon" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" version="1.1" width="24" height="24" viewBox="0 0 24 24"><path d="M17,9H7V7H17M17,13H7V11H17M14,17H7V15H14M12,3A1,1 0 0,1 13,4A1,1 0 0,1 12,5A1,1 0 0,1 11,4A1,1 0 0,1 12,3M19,3H14.82C14.4,1.84 13.3,1 12,1C10.7,1 9.6,1.84 9.18,3H5A2,2 0 0,0 3,5V19A2,2 0 0,0 5,21H19A2,2 0 0,0 21,19V5A2,2 0 0,0 19,3Z" /></svg>
                  </div>
                </div>
            </div>

            <h6 class="text-primary">Public Address (SHARE)</h6>
            <div class="input-group">
                <input
                  v-model="generatedWalletList"
                  class="text-dark h6 text-area p-3 font-secondary w600"
                  id="generatedpub"
                  readonly
                />
                <div class="input-group-append" @click="copypublic">
                  <div class="input-group-text">
                    <svg class="icon" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" version="1.1" width="24" height="24" viewBox="0 0 24 24"><path d="M17,9H7V7H17M17,13H7V11H17M14,17H7V15H14M12,3A1,1 0 0,1 13,4A1,1 0 0,1 12,5A1,1 0 0,1 11,4A1,1 0 0,1 12,3M19,3H14.82C14.4,1.84 13.3,1 12,1C10.7,1 9.6,1.84 9.18,3H5A2,2 0 0,0 3,5V19A2,2 0 0,0 5,21H19A2,2 0 0,0 21,19V5A2,2 0 0,0 19,3Z" /></svg>
                  </div>
                </div>
            </div>
          </div>
          <div class="col-12">
            <div class="row d-flex justify-content-center">
              <button
                class="btn btn-lg btn-my btn-my-shadow text-light w600 px-4 px-lg-5 mt-3"
                @click="printWallets"
              >PRINT PAPER WALLET</button>
            </div>
          </div>
          <div
            class="col-12 paper mt-4 px-4 py-2 px-lg-5 py-lg-4 mb-5"
            id="printableContent"
            ref="printableContent"
          >
            <div v-for="wallet in wallets" :key="wallet.address">
              <PaperWallet :design="wallet.design" :address="wallet.address" :seed="wallet.seed"/>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import Vue from "vue";
import PaperWallet from "./PaperWallet.vue";
import WalletGen from "../util/wallet_gen.ts";
import { Printd } from "printd";
import Notifications from 'vue-notification'

var printer = new Printd();
Vue.use(Notifications);
export default Vue.extend({
  name: "CreateGenerateBanner",
  data() {
    return {
      numPaperWallets: 1,
      design: "A",
      wallets: [],
      generatedWalletList: "",
      generatedWalletseed: ""
    };
  },
  methods: {

    copypublic: function (e) {
      let selectEl = document.querySelector('#generatedpub');

      selectEl.select();
      try {
            var successful = document.execCommand('copy');;
            alert('Address copied to clipboard successfully');
          } catch (err) {
            alert('Oops, unable to copy');
          }
    },
    copyseed: function (e) {
      let selectEl = document.querySelector('#generatedseed');

      selectEl.select();
      try {
            var successful = document.execCommand('copy');;
            alert('Seed copied to clipboard successfully');
          } catch (err) {
            alert('Oops, unable to copy');
          }
    },

    generateWallets() {

          WalletGen.genWallet().then(wallet => {

            this.wallets.push({
              address: wallet.address,
              seed: wallet.seed.toUpperCase(),
              design: this.design
            });

            this.generatedWalletList = wallet.address;
            this.generatedWalletseed = wallet.seed;


          });
          this.wallets =[];


      let title;
      if (this.design != 'G') {
        title = "Wallet has been generated!"
      } 

      this.$notify({
        group: "foo",
        title: title,
      });
    },
    printWallets() {
      let nativeElement = document.getElementById("nanopaperwallet-data");
      let overpassMonoPath = nativeElement.getAttribute("data-overpass-mono");
      printer.print(this.$refs.printableContent, [
        `
@font-face {
  font-family: "Overpass Mono";
  src: url("${overpassMonoPath}") format("truetype"); /* Safari, Android, iOS */
  font-weight: 700;
  font-style: normal;
}

@media print{@page {size: landscape}}

@page  
{ 
    /* this affects the margin in the printer settings */ 
    margin: 0.35in auto 0 auto;
}

.addressText {
    position: absolute;
    font-family: 'Overpass Mono', monospace;
    font-size: 5.7px;
    right: 0.205in;
    bottom: 0.5in;
    text-align: center;
}

.seedText {
    position: absolute;
    font-family: 'Overpass Mono', monospace;
    font-size: 5.7px;
    left: 1.02in;
    bottom: 1.25in;
    text-align: center;
    transform: rotate(90deg);
}

.addressTextColoredA {
    color: #2677FF;
}

.addressTextColoredB {
    color: #FF6C08;
}

.addressTextColoredC {
    color: #02B799;
}

.addressTextColoredD {
    color: #2677FF;
}
.addressTextColoredE {
    color: #2677FF;
}
.addressTextColoredF {
    color: #2677FF;
}

.wallet-container {
    width: 9.25in;
    height: 2.6in;
    position: relative;
}

.wallet-qr {
    position: absolute;
    width: 1.135in;
    height: 1.135in;
    background-color: white;
    top: 0.68in;
    left: 0.28in;
}

.address-qr {
    position: absolute;
    width: 1.135in;
    height: 1.135in;
    background-color: white;
    top: 0.68in;
    right: 0.24in;
}
.address-logo {
    width: 0.3986in;
    height: 0.3986in;
    position: absolute;
    top: 1.05in;
    left: 0.65in
}
.address-logo-right {
    width: 0.3986in;
    height: 0.3986in;
    position: absolute;
    top: 1.05in;
    right: 0.58in
}        
        `
      ]);
    }
  },
  components: {
    PaperWallet
  }
});
</script>