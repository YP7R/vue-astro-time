<script setup>
import { onMounted, onBeforeUnmount, computed, reactive, ref } from "vue";
import localSideralTime from "../sideralTime";

let timer = null;

let refnow = ref(Date.now());

let longitudeOGE = 6.131966;

onMounted(() => {
  timer = setInterval(() => {
    refnow.value = Date.now();
  }, 1000);
});

onBeforeUnmount(() => {
  clearInterval(timer);
});

// Options
let optionsHours = {
  minimumIntegerDigits: 2,
};

let optionsMinutes = {
  minimumIntegerDigits: 2,
};

let optionsSeconds = {
  minimumIntegerDigits: 2,
};
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
let computedLocalDate = computed(() => {
  return new Date(refnow.value).toLocaleString("fr-CH", optionsLocal);
});

let computedUtcDate = computed(() => {
  return new Date(refnow.value).toLocaleString("fr-CH", optionsUTC);
});

let computedSiderealTime = computed(() => {
  // Angle horaire
  return localSideralTime(longitudeOGE, new Date(refnow.value)).toFixed(4);
});

let computedDMSSiderealTime = computed(() => {
  let totalSeconds =
    localSideralTime(longitudeOGE, new Date(refnow.value)) * 60 * 60;
  let degree = Math.floor(totalSeconds / (60 * 60));
  let rest = totalSeconds % (60 * 60);
  let minutes = Math.floor(rest / 60);
  let seconds = rest % 60;
  
  let minutesFormatted = Math.floor(minutes).toLocaleString("fr-CH", optionsMinutes)
  let secondsFormatted = Math.floor(seconds).toLocaleString("fr-CH", optionsSeconds)
  
  return `${degree}° ${minutesFormatted}m ${secondsFormatted}s`;
});

let computedHMSSiderealTime = computed(() => {
  let angle = localSideralTime(longitudeOGE, new Date(refnow.value));
  let hours = Math.floor(angle / 15);
  let minutes = Math.floor((angle / 15 - hours) * 60);
  let seconds = (angle / 15 - hours - minutes / 60) * 3600;
  if (seconds == 60) {
    seconds = 0;
    minutes += 1;
  }
  if (minutes == 60) {
    minutes = 0;
    hours += 1;
  }
  hours = hours % 24;

  let hoursFormatted = hours.toLocaleString("fr-CH", optionsHours)
  let minutesFormatted = Math.floor(minutes).toLocaleString("fr-CH", optionsMinutes)
  let secondsFormatted = Math.floor(seconds).toLocaleString("fr-CH", optionsSeconds)
  return `${hoursFormatted}:${minutesFormatted}:${secondsFormatted}`
});

defineProps({
  msg: {
    type: String,
    required: true,
  },
});
</script>

<template>
  <div>
    Hello
    <p>{{ computedUtcDate }}</p>
    <p>{{ computedLocalDate }}</p>
    <p>{{ computedDMSSiderealTime }}</p>
    <p>{{ computedHMSSiderealTime }}</p>
  </div>
  <!---
  <div class="w-auto h-80 my-20">
    <div class="flex flex-row p-6 bg-black text-white font-mono text-center">
      <div class="basis-1 w-auto">
        <h1 class="text-2xl font-black">{{ computedUtcDate }}</h1>
        <h2 class="text-base text-[#989898] font-semibold">Temps universel</h2>
      </div>
      <div class="basis-1 w-auto">
        <h1 class="text-2xl font-black">{{ computedLocalDate }}</h1>
        <h2 class="text-base font-semibold text-[#989898]">Temps local</h2>
      </div>
    </div>
    <div class="flex flex-row p-6 bg-[#989898] text-black font-mono text-center">
      <div class="basis-1/1 w-full">
        <h1 class="text-4xl font-black"> {{computedHMSSiderealTime}}</h1>
        <h2 class="text-xl font-semibold text-white">Temps sidéral</h2>
      </div>
    </div>
  </div>
  -->
</template>

<style scoped>
</style>
