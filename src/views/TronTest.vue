<script setup lang="ts">
import { Ref, ref } from "vue-demi"

const tronWeb = (window as any).tronWeb
const address = tronWeb.defaultAddress.base58
const transferAddress = 'TNDrBYCjyxohQRfteRBdMzECCAhRcjewNV' // testing
const apiKey = 'd099c4d3-bdd4-45b6-a246-bb2745492653'
const sc = 'TKWsxjh97sCPipTAfghW7vDFdx2HhjecCe'
const info: Ref<any> = ref({})

// set header
tronWeb.setHeader({ 'TRON-PRO-API-KEY': apiKey })

// get contract
let contract = ref()
const getContract = async () => {
  contract.value = await tronWeb.contract().at(sc)
  contract.value.tronWeb.setHeader({ 'TRON-PRO-API-KEY': apiKey })
  console.log('contract', contract.value)
}

const getContractMethod = async (method: any) => {
  if (!contract.value) {
    await getContract()
  }
  if (!method) {
    alert('method cannot be empty')
  }
  const func = contract.value[method]
  if (func) {
    if (method == 'balanceOf') {
      const result = await func(address).call()
      info.value[method] = result.toString() / 100
    } else if(method == 'transfer') {
      try {
        const result = await func(transferAddress, 10000).send({ feeLimit: 5e9 })
        info.value[method] = result
      } catch (error) {
        info.value[method] = error
      }
    } else {
      const result = await func().call()
      info.value[method] = result
    }
  }
}
</script>

<template>
  <main>
    <h1>Tron Test</h1>
    <pre>tronWeb.ready: {{ tronWeb.ready }}</pre>
    <button @click="getContractMethod('name')">Get Name</button>
    <button @click="getContractMethod('symbol')">Get Symbol</button>
    <button @click="getContractMethod('decimals')">Get Decimals</button>
    <button @click="getContractMethod('balanceOf')">Get Balance</button>
    <button @click="getContractMethod('transfer')">Transfer</button>
    <pre>info: {{ info }}</pre>
  </main>
</template>
