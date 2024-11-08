<script setup>
import { onMounted, ref } from "vue";
import Stream from "./components/Stream.vue";

const readQrCode = ref(false);

function getMobileOperatingSystem() {
  var userAgent = navigator.userAgent || navigator.vendor || window.opera;
  if (
    /windows phone/i.test(userAgent) ||
    /android/i.test(userAgent) ||
    (/iPad|iPhone|iPod/.test(userAgent) && !window.MSStream)
  ) {
    readQrCode.value = true;
  }
}

onMounted(() => {
  getMobileOperatingSystem();
});
</script>

<template>
  <main class="container">
    <div class="row">
      <div class="flex column gap-1">
        <Stream v-if="readQrCode" />

        <div class="text-center" v-else>
          <h1 class="bold">ERRO!!!</h1>
          <p>
            A aplicação funciona apenas em dispositivos:
            <b>Android, iOS, Windows Phone</b>
          </p>
        </div>
      </div>
    </div>
  </main>
</template>

<style scoped></style>
