<template>
  <q-page class="flex flex-center column bg-info">
    <img
      alt="Quasar logo"
      src="/free-check-mark-icon-3280-thumb.png"
      style="width: 300px; height: 300px"
    />
    <div
      class="text-subtitle1 q-mx-lg q-my-xl text-white"
      style="text-align: center; text-shadow: 1px 1px black"
    >
      ¡Mensaje enviado con Exito!
    </div>
    <q-btn
      color="info"
      class="q-mb-xl"
      label="Enviar otro mensaje"
      @click="$router.push('/FormPage')"
    />
  </q-page>
</template>

<script>
import { defineComponent } from "vue";

export default defineComponent({
  name: "EndPage",
});
</script>
