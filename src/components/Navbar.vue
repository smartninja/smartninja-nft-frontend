<template>
  <nav class="navbar sticky-top navbar-expand-lg navbar-light bg-light">
    <div class="container-fluid">
      <router-link to="/" class="navbar-brand">Navbar</router-link>

      <button class="navbar-toggler" type="button" data-bs-toggle="collapse" data-bs-target="#navbarSupportedContent" aria-controls="navbarSupportedContent" aria-expanded="false" aria-label="Toggle navigation">
        <span class="navbar-toggler-icon"></span>
      </button>

      <div class="collapse navbar-collapse" id="navbarSupportedContent">
        <ul class="navbar-nav me-auto mb-2 mb-lg-0">

          <li class="nav-item">
            <router-link class="nav-link active" aria-current="page" to="/">Home</router-link>
          </li>

          <li class="nav-item">
            <router-link class="nav-link" to="/about">About</router-link>
          </li>

          <li class="nav-item">
            <router-link class="nav-link" to="/profile">Profile</router-link>
          </li>

        </ul>

        <div class="d-flex">
          <div v-if="isActivated" class="">
            <!-- Account -->
            <button class="btn btn-warning">
              {{ shortenAddress(address) }}
            </button>

            <button class="btn btn-warning">
              {{ displayEther(balance) }} ETH
            </button>
          </div>

          <button v-else class="btn btn-outline-success" @click="open">Connect wallet</button>
        </div>

      </div>
    </div>
  </nav>
</template>

<script lang="ts">
import { useBoard, useEthers, displayEther, shortenAddress } from 'vue-dapp'

export default {
  name: "Navbar",
  
  setup() {
    const { open } = useBoard()
    const { address, balance, isActivated } = useEthers()

    return {
      address, balance, isActivated, open, displayEther, shortenAddress
    }
  }
}
</script>
