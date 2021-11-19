<template>
  <div class="container text-center">
    <h2>Profile</h2>

    <p>Address: {{address}}</p>
    <p>Balance: {{ displayEther(balance) }} ETH</p>
  </div>
</template>

<script lang="ts">
import { useEthers, displayEther } from 'vue-dapp'

export default {
  name: "Profile",

  setup() {
    const { address, balance, isActivated } = useEthers();

    return {
      address, balance, isActivated, displayEther
    }
  }
}
</script>
