<script setup>
import { ref } from "vue";
import { QrcodeStream } from "vue-qrcode-reader";

const result = ref("");
const error = ref("");

function paintBoundingBox(detectedCodes, ctx) {
  for (const detectedCode of detectedCodes) {
    const {
      boundingBox: { x, y, width, height },
    } = detectedCode;

    ctx.lineWidth = 2;
    ctx.strokeStyle = "#007bff";
    ctx.strokeRect(x, y, width, height);
  }
}

function onError(err) {
  error.value = `[${err.name}]: `;

  if (err.name === "NotAllowedError") {
    error.value += "you need to grant camera access permission";
  } else if (err.name === "NotFoundError") {
    error.value += "no camera on this device";
  } else if (err.name === "NotSupportedError") {
    error.value += "secure context required (HTTPS, localhost)";
  } else if (err.name === "NotReadableError") {
    error.value += "is the camera already in use?";
  } else if (err.name === "OverconstrainedError") {
    error.value += "installed cameras are not suitable";
  } else if (err.name === "StreamApiNotSupportedError") {
    error.value += "Stream API is not supported in this browser";
  } else if (err.name === "InsecureContextError") {
    error.value +=
      "Camera access is only permitted in secure context. Use HTTPS or localhost rather than HTTP.";
  } else {
    error.value += err.message;
  }
}

function onDetect(detectedCodes) {
  result.value = JSON.stringify(detectedCodes.map((code) => code.rawValue));
}
</script>

<template>
  <div class="flex column gap-1">
    <QrcodeStream
      @detect="onDetect"
      @error="onError"
      class="reader"
      v-if="!result"
    />

    <div v-if="result" class="result">
      <p>
        Result:
        <b>{{ result }}</b>
      </p>

      <button @click="result=''">
        <svg
          width="20"
          height="20"
          viewBox="0 0 20 20"
          fill="none"
          xmlns="http://www.w3.org/2000/svg"
        >
          <path
            d="M3.463 2.43301C5.27756 0.86067 7.59899 -0.00333986 10 9.70266e-06C15.523 9.70266e-06 20 4.47701 20 10C20 12.136 19.33 14.116 18.19 15.74L15 10H18C18.0001 8.43163 17.5392 6.89781 16.6747 5.58927C15.8101 4.28072 14.5799 3.25517 13.1372 2.64013C11.6944 2.0251 10.1027 1.84771 8.55996 2.13003C7.0172 2.41234 5.59145 3.14191 4.46 4.22801L3.463 2.43301ZM16.537 17.567C14.7224 19.1393 12.401 20.0034 10 20C4.477 20 0 15.523 0 10C0 7.86401 0.67 5.88401 1.81 4.26001L5 10H2C1.99987 11.5684 2.46075 13.1022 3.32534 14.4108C4.18992 15.7193 5.42007 16.7449 6.86282 17.3599C8.30557 17.9749 9.89729 18.1523 11.44 17.87C12.9828 17.5877 14.4085 16.8581 15.54 15.772L16.537 17.567Z"
            fill="white"
          />
        </svg>
      </button>
    </div>
  </div>
</template>

<style lang="scss">
.reader {
  width: 80dvw !important;
  height: 80dvh !important;
}

.result {
  background: #ddd;
  padding: 1.5rem;
  border-radius: 16px;
  text-align: center;
  display: grid;
  grid-template-columns: 1fr 50px;
  gap: 1rem;
  align-items: center;

  button {
    border: 0;
    border-radius: inherit;
    background: #4a00b9;
    width: 50px;
    height: 50px;
    display: flex;
    justify-content: center;
    align-items: center;

    svg {
      width: 50%;
      height: 50%;
    }
  }
}
</style>
