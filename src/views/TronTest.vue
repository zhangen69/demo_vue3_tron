<script setup lang="ts">
import { Ref, ref } from "vue-demi";
import axios from 'axios';
import qs from 'qs';

// setup axios configs
axios.defaults.headers.common['Content-Type'] = 'application/x-www-form-urlencoded';
axios.defaults.headers.post['Content-Type'] = 'application/x-www-form-urlencoded';
axios.defaults.headers.get['Content-Type'] = 'application/x-www-form-urlencoded';
// write some useless sample header
axios.defaults.headers.common['Sample-Config'] = 'this is a sample config';

const sampleData = {
    "name": "morpheus",
    "job": "leader"
}

const sampleApiURL = 'https://reqres.in/api/users'

const tronWeb = (window as any).tronWeb;
const defaultAddress = tronWeb.defaultAddress.base58;
const transferAddress = "TNDrBYCjyxohQRfteRBdMzECCAhRcjewNV"; // testing
const apiKey = "d099c4d3-bdd4-45b6-a246-bb2745492653";
const sc = "TKWsxjh97sCPipTAfghW7vDFdx2HhjecCe";
const info: Ref<any> = ref({});

// set header
tronWeb.setHeader({ "TRON-PRO-API-KEY": apiKey, "Content-Type": 'application/x-www-form-urlencoded' });

// get contract
let contract = ref();
const getContract = async () => {
  contract.value = await tronWeb.contract().at(sc);
  contract.value.tronWeb.setHeader({ "TRON-PRO-API-KEY": apiKey, "Content-Type": 'application/x-www-form-urlencoded' });
  console.log("contract", contract.value);
};

async function testSampleAPI(type: string) {
  if (type == 'post') {
    const res = await axios.post(sampleApiURL, qs.stringify(sampleData))
    console.log('sample post api', res)
  } else {
    const res = await axios.get(sampleApiURL)
    console.log('sample get api', res)
  }
}

async function testName() {
  // check contract
  if (!contract.value) {
    await getContract();
  }

  // call contract method
  try {
    contract.value.tronWeb.setHeader({ "TRON-PRO-API-KEY": apiKey, "Content-Type": 'application/x-www-form-urlencoded' });
    const result = await contract.value.name().call();
    console.log('testName', result)
    info.value.name = result;
  } catch (error) {
    info.value.name = error;
    console.log('testName err', error)
  }
}

async function testSymbol() {
  // check contract
  if (!contract.value) {
    await getContract();
  }

  // call contract method
  try {
    contract.value.tronWeb.setHeader({ "TRON-PRO-API-KEY": apiKey, "Content-Type": 'application/x-www-form-urlencoded' });
    const result = await contract.value.symbol().call();
    console.log('testSymbol', result)
    info.value.symbol = result;
  } catch (error) {
    info.value.symbol = error;
    console.log('testSymbol err', error)
  }
}

async function testDecimals() {
  // check contract
  if (!contract.value) {
    await getContract();
  }

  // call contract method
  try {
    contract.value.tronWeb.setHeader({ "TRON-PRO-API-KEY": apiKey, "Content-Type": 'application/x-www-form-urlencoded' });
    const result = await contract.value.decimals().call();
    console.log('testDecimals', result)
    info.value.decimals = result;
  } catch (error) {
    info.value.decimals = error;
    console.log('testDecimals err', error)
  }
}

async function testBalanceOf() {
  // check contract
  if (!contract.value) {
    await getContract();
  }

  // call contract method
  try {
    contract.value.tronWeb.setHeader({ "TRON-PRO-API-KEY": apiKey, "Content-Type": 'application/x-www-form-urlencoded' });
    const result = await contract.value.balanceOf(defaultAddress).call();
    console.log('testBalanceOf', result)
    info.value.balance = result;
  } catch (error) {
    info.value.balance = error;
    console.log('testBalanceOf err', error)
  }
}

async function testTransfer() {
  // check contract
  if (!contract.value) {
    await getContract();
  }

  // call contract method
  try {
    contract.value.tronWeb.setHeader({ "TRON-PRO-API-KEY": apiKey, "Content-Type": 'application/x-www-form-urlencoded' });
    const result = await contract.value
      .transfer(transferAddress, 1000)
      .send({ feeLimit: 5e9 });
    console.log('testTransfer', result)
    info.value.transfer = result;
  } catch (error) {
    info.value.transfer = error;
    console.log('testTransfer err', error)
  }
}

async function getNodeInfo() {
  // call get node info
  try {
    tronWeb.setHeader({ "TRON-PRO-API-KEY": apiKey, "Content-Type": 'application/x-www-form-urlencoded' });
    const result = await tronWeb.trx.getNodeInfo();
    console.log('getNodeInfo', result)
    info.value.nodeInfo = result;
  } catch (error) {
    info.value.nodeInfo = error;
    console.log('getNodeInfo err', error)
  }
}
</script>

<template>
  <main>
    <h1>Tron Test</h1>
    <pre>tronWeb.ready: {{ tronWeb.ready }}</pre>
    <button @click="getNodeInfo">Get NodeInfo</button>
    <button @click="testName">Get Name</button>
    <button @click="testSymbol">Get Symbol</button>
    <button @click="testDecimals">Get Decimals</button>
    <button @click="testBalanceOf">Get Balance</button>
    <button @click="testTransfer">Transfer</button>
    <button @click="testSampleAPI('post')">Test POST API</button>
    <button @click="testSampleAPI('get')">Test GET API</button>
    <pre>info: {{ info }}</pre>
  </main>
</template>
