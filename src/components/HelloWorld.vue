<script setup>
import { onMounted, onBeforeUnmount, computed, reactive, ref } from "vue";
import localSideralTime from '../sideralTime'

let timer = null;
let now = Date.now();

let refnow = ref(Date.now())

onMounted(() => {
  timer = setInterval(() => {
    refnow.value = Date.now();
  }, 1000);
});

onBeforeUnmount(() => {
  clearInterval(timer);
});

// Options 
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

//Computed property
let localDate = new Date(now).toLocaleString("fr-CH", optionsLocal);
let utcDate = new Date(now).toLocaleString("fr-CH", optionsUTC);
let sideralTime = localSideralTime(6.131966, new Date(now));

let computedLocalDate = computed(() => {
  return new Date(refnow.value).toLocaleString('fr-CH', optionsLocal);
});

let computedUtcDate = computed(()=>{
  return new Date(refnow.value).toLocaleString('fr-CH', optionsUTC);
})

let computedSiderealTime = computed(()=>{
  return localSideralTime(6.131966, new Date(refnow.value)).toFixed(4);
})

defineProps({
  msg: {
    type: String,
    required: true,
  },
});
</script>

<template>
  <div class="w-auto h-80 my-20">
    <div class="flex flex-row p-6 bg-black text-white font-mono text-center">
      <div class="basis-1/2 w-auto">
        <h1 class="text-2xl font-black">{{ computedUtcDate }}</h1>
        <h2 class="text-base text-[#989898] font-semibold">Temps universel</h2>
      </div>
      <div class="basis-1/2 w-auto">
        <h1 class="text-2xl font-black">{{ computedLocalDate }}</h1>
        <h2 class="text-base font-semibold text-[#989898]">Temps local</h2>
      </div>
    </div>
    <div class="flex flex-row p-6 bg-[#989898] text-black font-mono text-center">
      <div class="basis-1/1 w-full">
        <h1 class="text-4xl font-black">{{ computedSiderealTime }} [unité ?!]</h1>
        <h2 class="text-xl font-semibold text-white">Temps sidéral</h2>
      </div>
    </div>
  </div>
</template>

<style scoped>
</style>
