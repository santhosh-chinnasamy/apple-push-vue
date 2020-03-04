<template>
  <v-app id="inspire">
    <v-content>
      <v-container class="fill-height" fluid>
        <v-row align="center" justify="center">
          <v-col cols="12" sm="8" md="8">
            <v-card class="elevation-12">
              <v-toolbar color="primary" dark flat>
                <v-toolbar-title>APNS</v-toolbar-title>
                <v-spacer />
              </v-toolbar>
              <v-card-text>
                <v-form
                  ref="form"
                  v-model="valid"
                  lazy-validation
                  enctype="multipart/form-data"
                >
                  <v-text-field
                    label="KeyId"
                    name="keyId"
                    v-model="keyId"
                    :rules="keyIdRules"
                    prepend-icon="mdi-key-outline"
                    type="text"
                  />

                  <v-text-field
                    id="teamId"
                    label="TeamId"
                    name="teamId"
                    :counter="10"
                    v-model="teamId"
                    :rules="teamIdRules"
                    prepend-icon="mdi-account-supervisor-outline"
                    type="text"
                  />
                  <v-text-field
                    id="deviceToken"
                    label="deviceToken"
                    name="deviceToken"
                    v-model="deviceToken"
                    :rules="deviceTokenRules"
                    prepend-icon="mdi-cellphone-iphone"
                    type="text"
                  />
                  <v-text-field
                    id="alertMessage"
                    label="alertMessage"
                    name="alertMessage"
                    v-model="alertMessage"
                    :rules="messageRules"
                    prepend-icon="mdi-message-outline"
                    type="text"
                  />
                  <v-text-field
                    id="alertPayload"
                    label="alertPayload"
                    name="alertPayload"
                    v-model="alertPayload"
                    :rules="payloadRules"
                    prepend-icon="mdi-code-braces"
                    type="text"
                  />
                  <v-text-field
                    id="bundleId"
                    label="Bundle Id"
                    name="bundleId"
                    v-model="bundleId"
                    :rules="bundleIdRules"
                    prepend-icon="mdi-apple-ios"
                    type="text"
                  />
                  <v-file-input
                    small-chips
                    label="Choose certificate"
                    name="certificate"
                    v-model="certificate"
                    accept=".p8"
                    :rules="certificateRules"
                    ref="certfile"
                    @change="onSelect"
                  ></v-file-input>
                  <!-- <input
                    type="file"
                    name="certificate"
                    id="certificate"
                    accept=".p8"
                    :rules="certificateRules"
                    ref="certfile"
                    @change="onSelect"
                  /> -->
                </v-form>
              </v-card-text>
              <v-card-actions>
                <v-spacer />
                <!-- eslint-disable-next-line -->
                <v-btn color="primary" :disabled="!valid" @click="validate">Send Push</v-btn>
              </v-card-actions>
            </v-card>
          </v-col>
        </v-row>
        <v-row>
          <v-col>
            <v-card>{{ result }}</v-card>
          </v-col>
        </v-row>
      </v-container>
    </v-content>
  </v-app>
</template>

<script>
import axios from "axios";

export default {
  name: "ApplePushForm",
  data: () => ({
    valid: true,
    result: null,
    keyId: null,
    teamId: null,
    deviceToken: null,
    alertMessage: null,
    alertPayload: null,
    bundleId: null,
    certificate: null,
    // Validation rules
    keyIdRules: [v => !!v || "Key Id is required"],
    teamIdRules: [v => !!v || "Team Id is required"],
    messageRules: [v => !!v || "Message is required"],
    deviceTokenRules: [v => !!v || "Device Token is required"],
    payloadRules: [v => !!v || "Payload is required"],
    bundleIdRules: [v => !!v || "Bundle Id is required"],
    certificateRules: [v => !!v || "Certificate is required"]
  }),
  methods: {
    onSelect() {
      // this.certificate = event.target.files[0];
      // // console.log("file", this.certificate);
      // if (this.certificate) {
      //   console.log(this.certificate);
      // }
      this.certificate = this.$refs.certfile.files[0];
      if (this.certificate) {
        console.log(this.certificate);
      }
    },
    validate() {
      if (this.$refs.form.validate()) {
        let data = {
          keyId: this.keyId,
          teamId: this.teamId,
          deviceToken: this.deviceToken,
          alertMessage: this.alertMessage,
          alertPayload: this.alertPayload,
          alertTopic: this.bundleId
        };
        data.files.certificate = this.certificate;
        console.log("cert", data);
        axios.defaults.headers.post["Content-Type"] = "multipart/form-data";
        axios.defaults.headers.common["Access-Control-Allow-Origin"] = "*";

        axios
          // .post(`https://apns-push.herokuapp.com/apn`, data)
          .post(`http://localhost:9000/apn`, data)
          .then(response => {
            this.result = response;
            console.log("response", this.result);
          })
          .catch(e => {
            console.log(e);
          });
      }
    }
  }
};
</script>
