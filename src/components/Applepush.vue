<template>
  <v-app id="inspire">
    <v-content>
      <v-container class="fill-height" fluid>
        <v-row align="center" justify="center">
          <v-col cols="12" sm="8" md="6">
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
                    @change="onFileChanged"
                  ></v-file-input>
                  <v-switch
                    v-model="isProduction"
                    :label="`Production Mode: ${isProduction.toString()}`"
                    name="certificate"
                  ></v-switch>
                </v-form>
              </v-card-text>
              <v-snackbar
                v-model="snackbar"
                :multi-line="multiLine"
                :top="y === 'top'"
              >
                {{ message }} {{ result }}
                <v-btn color="red" text @click="snackbar = false">Close</v-btn>
              </v-snackbar>
              <v-card-actions>
                <v-spacer />
                <v-btn color="primary" :disabled="!valid" @click="validate"
                  >Send Push</v-btn
                >
              </v-card-actions>
            </v-card>
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
    isProduction: false,
    multiLine: true,
    snackbar: false,
    message: null,
    y: "top",
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
    onFileChanged(event) {
      this.certificate = event;
    },
    validate() {
      if (this.$refs.form.validate()) {
        const formData = new FormData();
        formData.append("keyId", this.keyId);
        formData.append("teamId", this.teamId);
        formData.append("deviceToken", this.deviceToken);
        formData.append("alertMessage", this.alertMessage);
        formData.append("alertPayload", this.alertPayload);
        formData.append("alertTopic", this.bundleId);
        formData.append("isProduction", this.isProduction);
        formData.append("certificate", this.certificate);

        axios
          .post("https://apns-push.herokuapp.com/apn", formData)
          .then(response => {
            this.snackbar = true;
            if (response.data.sent.length > 0) {
              response.data.sent.message = "Success";
              this.result = response.data.sent;
              this.message = "Success";
            } else if (response.data.failed.length > 0) {
              this.result = response.data.failed;
              this.message = "Failed";
            } else {
              this.result = "Something Went Wrong!";
            }
          })
          .catch(e => {
            this.snackbar = true;
            this.result = "P8 Tester Not Working!";
          });
      }
    }
  }
};
</script>
