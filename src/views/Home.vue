<template>
  <div class="text-center">
    <h1 class="text-uppercase main-title">Discover your SmartNinja NFTs</h1>

    <!-- Connect Wallet / Show Address -->
    <div v-if="isActivated" class="">
      <button class="btn btn-outline-success" @click="disconnect">
        {{ shortenAddress(address) }}
      </button>
    </div>

    <button v-else class="btn btn-outline-success" @click="open">Connect Wallet</button>
    <!-- END Connect Wallet / Show Address -->

    <h4 class="subtitle" v-if="!isActivated">Connect your wallet to find your SmartNinja NFT.</h4>
    <h4 class="subtitle" v-if="isActivated">Here's your SmartNinja NFT.</h4>

    <div v-if="isActivated" class="card mx-auto card-nft">
      <img src="https://storage.googleapis.com/smartninja/nft-smartninja-genesis-1637152368.gif" class="card-img-top" alt="SmartNinja NFT">
      <div class="card-body text-start">
        <h5 class="card-title">SmartNinja</h5>
        <p class="card-text">Genesis</p>
      </div>
    </div>

    <div v-if="!isActivated" class="card mx-auto card-nft card-nft-questionmark" style="width: 18rem;">
      <div class="card-body nft-questionmark d-flex align-items-center justify-content-center">
        <p>?</p>
      </div>
    </div>

    <div class="spacer"></div>

  </div>
</template>

<script lang="ts">
import { useBoard, useEthers, useWallet, shortenAddress } from 'vue-dapp';

export default {
  name: "Home",

  setup() {
    const { open } = useBoard()
    const { disconnect } = useWallet()
    const { address, isActivated } = useEthers()

    return {
      address, disconnect, isActivated, open, shortenAddress
    }
  }
}

</script>
