<script setup>
import { onMounted, onBeforeUnmount, computed, reactive } from "vue";
import localSideralTime from '../sideralTime'
let timer = null;
let reactiveNow = reactive({ now: Date.now() });
let now = Date.now();

onMounted(() => {
  timer = setInterval(() => {
    reactiveNow.now = Date.now();
  }, 1000);
});

onBeforeUnmount(() => {
  clearInterval(timer);
});


let options = {
  weekday: "long",
  year: "numeric",
  month: "long",
  day: "numeric",
  hour: "2-digit",
  minute: "2-digit",
  second: "2-digit",
  timeZoneName: "short",
  //fractionalSecondDigits : 0,
  hc: "h24",
};
let optionsLocal = { ...options, timeZone: "Europe/Zurich" };
let optionsUTC = { ...options, timeZone: "UTC" };

let localDate = new Date(now).toLocaleString("fr-CH", optionsLocal);
let computedLocalDate = computed(() => {
  new Date(reactiveNow.now).toLocaleString("fr-CH", optionsLocal);
});

let utcDate = new Date(now).toLocaleString("fr-CH", optionsUTC);
let sideralTime = localSideralTime(6.131966, new Date(now));
defineProps({
  msg: {
    type: String,
    required: true,
  },
});
</script>

<template>
  <div class="w-auto h-auto">
    <div class="flex flex-row p-6 bg-black text-white font-mono text-center">
      <div class="basis-1/2 w-auto">
        <h1 class="text-2xl font-black">{{ utcDate }}</h1>
        <h2 class="text-base text-[#989898] font-semibold">Temps universel</h2>
      </div>
      <div class="basis-1/2 w-auto">
        <h1 class="text-2xl font-black">{{ localDate }}</h1>
        <h2 class="text-base font-semibold text-[#989898]">Temps local</h2>
      </div>
    </div>
    <div class="flex flex-row p-6 bg-[#989898] text-black font-mono text-center">
      <div class="basis-1/1 w-full">
        <h1 class="text-4xl font-black">{{ sideralTime.toFixed(4) }} [unité ?!]</h1>
        <h2 class="text-xl font-semibold text-white">Temps sidéral</h2>
      </div>
    </div>
  </div>
</template>

<style scoped>
</style>
