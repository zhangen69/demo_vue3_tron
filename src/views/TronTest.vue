<script setup lang="ts">
import { Ref, ref } from "vue-demi";
import axios from 'axios';

// setup axios configs
axios.defaults.headers.post['Content-Type'] = 'application/x-www-form-urlencoded';

const tronWeb = (window as any).tronWeb;
const defaultAddress = tronWeb.defaultAddress.base58;
const transferAddress = "TNDrBYCjyxohQRfteRBdMzECCAhRcjewNV"; // testing
const apiKey = "d099c4d3-bdd4-45b6-a246-bb2745492653";
const sc = "TKWsxjh97sCPipTAfghW7vDFdx2HhjecCe";
const info: Ref<any> = ref({});

// set header
tronWeb.setHeader({ "TRON-PRO-API-KEY": apiKey });
tronWeb.setHeader({ "Content-Type": 'application/x-www-form-urlencoded' });

// get contract
let contract = ref();
const getContract = async () => {
  contract.value = await tronWeb.contract().at(sc);
  contract.value.tronWeb.setHeader({ "TRON-PRO-API-KEY": apiKey, "Content-Type": 'application/x-www-form-urlencoded' });
  console.log("contract", contract.value);
};

const getContractMethod = async (method: any) => {
  if (!contract.value) {
    await getContract();
  }
  if (!method) {
    alert("method cannot be empty");
  }
  const func = contract.value[method];
  if (func) {
    if (method == "balanceOf") {
      const result = await func(defaultAddress).call();
      info.value[method] = result.toString() / 100;
    } else if (method == "transfer") {
      try {
        const result = await func(transferAddress, 10000).send({
          feeLimit: 5e9,
        });
        info.value[method] = result;
      } catch (error) {
        info.value[method] = error;
      }
    } else {
      const result = await func().call();
      info.value[method] = result;
    }
  }
};

async function testName() {
  // check contract
  if (!contract.value) {
    await getContract();
  }

  // call contract method
  try {
    contract.value.tronWeb.setHeader({ "TRON-PRO-API-KEY": apiKey, "Content-Type": 'application/x-www-form-urlencoded' });
    const result = await contract.value.name().call();
    info.value.name = result;
  } catch (error) {
    info.value.name = error;
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
    info.value.symbol = result;
  } catch (error) {
    info.value.symbol = error;
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
    info.value.decimals = result;
  } catch (error) {
    info.value.decimals = error;
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
    info.value.balance = result;
  } catch (error) {
    info.value.balance = error;
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
    info.value.transfer = result;
  } catch (error) {
    info.value.transfer = error;
  }
}
</script>

<template>
  <main>
    <h1>Tron Test</h1>
    <pre>tronWeb.ready: {{ tronWeb.ready }}</pre>
    <button @click="testName">Get Name</button>
    <button @click="testSymbol">Get Symbol</button>
    <button @click="testDecimals">Get Decimals</button>
    <button @click="testBalanceOf">Get Balance</button>
    <button @click="testTransfer">Transfer</button>
    <pre>info: {{ info }}</pre>
  </main>
</template>
