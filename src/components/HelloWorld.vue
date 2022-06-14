<script setup>

let localSideralTime = (longitude, dateNow) => {
  let datetime = dateNow,
    Y = datetime.getUTCFullYear(),
    M = datetime.getUTCMonth() + 1, //en javascript : janvier = 0 !
    D = datetime.getUTCDate(),
    h = datetime.getUTCHours(),
    m = datetime.getUTCMinutes(),
    s = datetime.getUTCSeconds();

  D += h / 24 + m / 60 / 24 + s / 3600 / 24; //pour avoir la fraction du jour sous forme décimale
  return sideralTimeGreewich(julianDay(D, M, Y)) + longitude; // longitude > 0 --> Est
};

let julianDay = (D, M, Y) => {
  var A, B;

  if (M <= 2) {
    M += 12;
    Y -= 1;
  }

  A = Math.trunc(Y / 100);

  if (Y < 1582) B = 0;
  //si l'année est une date dans le calendrier julien
  else B = Math.trunc(2 - A + Math.trunc(A / 4));

  return (
    Math.trunc(365.25 * (Y + 4716)) +
    Math.trunc(30.6001 * (M + 1)) +
    D +
    B -
    1524.5
  );
};

let sideralTimeGreewich = (julianday) => {
  var T = (julianday - 2451545.0) / 36525,
    temp =
      (+280.46061837 +
        360.98564736629 * (julianday - 2451545) +
        0.000387933 * T * T -
        (T * T * T) / 38710000) %
      360; //opération modulo peut donner un résultat négatif...
  if (temp < 0) temp += 360; //...si c'est le cas, ajouter 360°

  return temp;
};
let now = Date.now();
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
let utcDate = new Date(now).toLocaleString("fr-CH", optionsUTC);
let sideralTime = localSideralTime(6.131966, new Date(now))
defineProps({
  msg: {
    type: String,
    required: true,
  },
});


</script>

<template>
    <h1 class="green">UTC : {{ utcDate }}</h1>
    <h1>Local : {{ localDate }}</h1>
    <h1>Temps sidéral : {{ sideralTime }}</h1>

</template>

<style scoped>
</style>
