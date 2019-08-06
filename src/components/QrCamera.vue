<template>
  <div id="qrcamera">
    <button @click="reload">Reset</button>
    <p>
      Last result:
      <b>{{ decodedContent }}</b>
    </p>

    <p class="error">{{ errorMessage }}</p>
    <p class="error" v-if="noFrontCamera">You don't seem to have a front camera on your device</p>

    <p class="error" v-if="noRearCamera">You don't seem to have a rear camera on your device</p>
    <qrcode-stream :camera="camera" @decode="onDecode" @init="onInit" v-if="!destroyed">
      <button @click="switchCamera">Switch Camera</button>
      <div class="loading-indicator" v-if="loading">Loading...</div>
    </qrcode-stream>
  </div>
</template>

<script>
export default {
  name: "qrcamera",
  data() {
    return {
      camera: "front",

      noRearCamera: false,
      noFrontCamera: false,
      loading: false,
      destroyed: false,
      decodedContent: "",
      errorMessage: ""
    };
  },
  methods: {
    onDecode(content) {
      this.decodedContent = content;
    },

    async onInit(promise) {
      this.loading = true;
      await promise
        .then(() => {
          console.log("Successfully initilized! Ready for scanning now!");
        })
        .catch(error => {
          const triedFrontCamera = this.camera === "front";
          const triedRearCamera = this.camera === "rear";
          const cameraMissingError = error.name === "OverconstrainedError";
          if (triedRearCamera && cameraMissingError) {
            this.noRearCamera = true;
          }

          if (triedFrontCamera && cameraMissingError) {
            this.noFrontCamera = true;
          }
          if (error.name === "NotAllowedError") {
            this.errorMessage = "Hey! I need access to your camera";
          } else if (error.name === "NotFoundError") {
            this.errorMessage = "Do you even have a camera on your device?";
          } else if (error.name === "NotSupportedError") {
            this.errorMessage =
              "Seems like this page is served in non-secure context (HTTPS, localhost or file://)";
          } else if (error.name === "NotReadableError") {
            this.errorMessage =
              "Couldn't access your camera. Is it already in use?";
          } else if (error.name === "OverconstrainedError") {
            this.errorMessage =
              "Constraints don't match any installed camera. Did you asked for the front camera although there is none?";
          } else {
            this.errorMessage = "UNKNOWN ERROR: " + error.message;
          }
        })
        .finally(() => {
          this.loading = false;
        });
    },
    async reload() {
      this.destroyed = true;
      this.decodedContent = "";
      await this.$nextTick();
      this.destroyed = false;
    },
    switchCamera() {
      switch (this.camera) {
        case "front":
          this.camera = "rear";
          break;
        case "rear":
          this.camera = "front";
          break;
      }
    }
  }
};
</script>

<style>

</style>
