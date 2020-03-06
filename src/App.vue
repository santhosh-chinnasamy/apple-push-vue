<template>
  <v-app>
    <v-app-bar app color="primary" dark>
      <div class="d-flex align-center">
        <v-icon>mdi-apple</v-icon>
        <span class="mr-2">APNS p8 tester</span>
      </div>

      <v-spacer></v-spacer>

      <v-btn
        href="https://github.com/santhosh-chinnasamy/apple-push-vue"
        target="_blank"
        text
      >
        <span class="mr-2">Github</span>
        <v-icon>mdi-github</v-icon>
      </v-btn>
    </v-app-bar>

    <v-content>
      <ApplePushForm />
    </v-content>
  </v-app>
</template>

<script>
import ApplePushForm from "./components/Applepush";

export default {
  name: "App",

  components: {
    ApplePushForm
  }
};
</script>
