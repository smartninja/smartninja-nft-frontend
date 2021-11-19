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

    <!-- if user connected AND has the SmartNinja NFT -->
    <div v-if="isActivated && !wrongChain && hasNft">
      <h4 class="subtitle">Here's your SmartNinja NFT.</h4>

      <div class="card mx-auto card-nft">
        <img src="https://storage.googleapis.com/smartninja/nft-smartninja-genesis-1637152368.gif" class="card-img-top" alt="SmartNinja NFT">
        <div class="card-body text-start">
          <h5 class="card-title">SmartNinja</h5>
          <p class="card-text">Genesis</p>
        </div>
      </div>
    </div>

    <!-- if user connected but does NOT HAVE the SmartNinja NFT -->
    <div v-if="isActivated && !wrongChain && !hasNft">
      <h4 class="subtitle">Looks like you don't have any SmartNinja NFT yet...</h4>

      <div class="card mx-auto card-nft card-nft-questionmark" style="width: 18rem;">
        <div class="card-body nft-questionmark d-flex align-items-center justify-content-center">
          <p><i class="bi bi-emoji-frown"></i></p>
        </div>
      </div>
    </div>

    <!-- if user not connected -->
    <div v-if="!isActivated">
      <h4 class="subtitle">Connect your wallet to see your SmartNinja NFT.</h4>

      <div class="card mx-auto card-nft card-nft-questionmark" style="width: 18rem;">
        <div class="card-body nft-questionmark d-flex align-items-center justify-content-center">
          <p>?</p>
        </div>
      </div>
    </div>

    <!-- if user on wrong chain -->
    <div v-if="isActivated && wrongChain">
      <h4 class="subtitle">
        You are on an unsupported network. Please switch to Polygon.
      </h4>

      <button class="btn btn-outline-success mt-5" @click="switchToPolygon">Click here to switch to Polygon</button>
    </div>

    <div class="spacer"></div>

  </div>
</template>

<script lang="ts">
import { useBoard, useEthers, useWallet, shortenAddress } from 'vue-dapp';
import SmartNinjaGenesisNft from "../assets/SmartNinjaGenesisNft.json";
import { ethers } from "ethers";

export default {
  name: "Home",

  data() {
    return {
      hasNft: false,
      wrongChain: true,
      chainName: null
    }
  },

  methods: {
    checkIfWrongChain() {
      if (this.isActivated) {
        if (this.chainId === 137) {
          this.chainName = "Polygon";
          this.wrongChain = false;
        } else if (this.chainId === 4) {
          this.chainName = "Rinkeby Testnet";
          this.wrongChain = false;
        } else {
          this.chainName = "unsupported chain";
          this.wrongChain = true;
        }
      }

      console.log("ChainId: " + this.chainId);
      console.log("Chain name: " + this.chainName);
      console.log("Wrong chain? " + this.wrongChain);
    },

    async fetchBalanceOf() {
      if (this.isActivated && !this.wrongChain) {
        // prepare the SmartNinja Genesis contract
        const contractInterface = new ethers.utils.Interface(SmartNinjaGenesisNft.abi);
      
        let contractAddress = null;

        if (this.chainId === 137) { // Polygon
          contractAddress = null;
        } else if (this.chainId === 4) { // Rinkeby
          contractAddress = "0xdf2f2419619a6296dc5a994a6213ad018f03d299";
        }

        if (contractAddress) {
          const contract = new ethers.Contract(contractAddress, contractInterface, this.provider);

          // check how many SmartNinja Genesis NFTs a user has
          const nftAmount = await contract.balanceOf(this.address);

          console.log("nftAmount: " + nftAmount);

          if (nftAmount > 0) {
            this.hasNft = true;
          } else {
            this.hasNft = false;
          }
        }

      }
    },

    switchToPolygon() {
      const params = [{
        chainId: '0x89',
        chainName: 'Polygon PoS Chain',
        nativeCurrency: {
          name: 'MATIC',
          symbol: 'MATIC',
          decimals: 18
        },
        rpcUrls: ['https://polygon-rpc.com/'],
        blockExplorerUrls: ['https://polygonscan.com/']
      }]

      this.provider.send('wallet_addEthereumChain', params) // this.provider.send instead of window.ethereum.request
        .then(() => console.log('Success'))
        .catch((error: Error) => console.log("Error", error.message))

      this.fetchBalanceOf();
    }
  },

  setup() {
    const { open } = useBoard();
    const { disconnect } = useWallet();
    const { address, chainId, isActivated, provider } = useEthers();

    return {
      address, chainId, disconnect, isActivated, open, provider, shortenAddress
    }
  },

  watch: {
    address: function() {
      this.fetchBalanceOf();
    },

    isActivated: function() {
      this.checkIfWrongChain();
      this.fetchBalanceOf();
    },

    chainId: function() {
      this.checkIfWrongChain();
      this.fetchBalanceOf();
    },
  }
}

</script>
