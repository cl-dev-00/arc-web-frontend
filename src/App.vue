<template>
  <v-app>
    <v-main>
      <router-view />
    </v-main>
  </v-app>
</template>

<script>
import { decodeToken } from "./helpers/decode-token";

export default {
  name: "App",
  mounted() {
    const tokenLocal = localStorage.getItem("token");

    if (tokenLocal) {
      this.$services.api
        .verify(tokenLocal)
        .then((response) => {

          if (response.data.success) {
            
            this.$services.socketio.initialize();

            const token = response.data.token;

            const tokenData = decodeToken(token);

            this.$store.dispatch("setUserAction", tokenData.data);

            localStorage.setItem("token", token);
          }
        })
        .catch(() => {
          this.$store.dispatch("setUserAction", null);

          localStorage.removeItem("token");

        });
    } else {
      this.$store.dispatch("setUserAction", null);

    }
  },
};
</script>
