<template>
  <div class="mt-16 flex justify-between">
    <p class="font-wheightbold text-sm">PAYMENT</p>

    <p class="font-wheightbold text-[7pt]">SECURE ENCRYPTED TRANSACTION</p>
  </div>
  <div class="border border-black mt-2 mb-3" />

  <button class="mt-8 border border-black w-fit" @click="pay" v-if="useShippingStore().isShippingDataSubmitted">
    <img
      src="/images/paypal.webp"
      alt="PayPal payment image"
      class="img-payment"
    />
  </button>
</template>

<script setup>
import axios from "axios";
import { useUserStore } from "@/stores/UserStore";
import { useShippingStore } from "@/stores/ShippingStore";

function pay() {
  const url = new URL(
    useRuntimeConfig().public.APP_URL + "/proxy/payments/paypal/initialize"
  );
  const data = {
    user_hash: !useUserStore().isLoggedIn ? useCookie("userHash").value : null,
    user_id: useUserStore().isLoggedIn ? useUserStore().authUser.id : null,
  };

  return axios.post(url.toString(), data).then((response) => {
    window.open(response.data.data, "_blank");
  });
}

onMounted(() => {
  useShippingStore().checkShippingData();
});
</script>

<style scoped>
.img-payment {
  width: 280px;
  cursor: pointer;
}
</style>
