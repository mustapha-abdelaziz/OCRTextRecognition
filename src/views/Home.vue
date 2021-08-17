<template>
  <div class="q-pa-md fixed-center">
    <div class="q-gutter-y-md" style="min-width: 700px">
      <q-card class="shadow">
        <q-tabs
          v-model="tab"
          dense
          class="text-grey"
          active-color="primary"
          indicator-color="primary"
          align="justify"
          narrow-indicator
          inline-label
          no-caps
        >
          <q-tab icon="file_upload" name="imageUpload" label="Upload a file" />
          <q-tab icon="link" name="urlUpload" label="Paste a URL" />
        </q-tabs>

        <q-tab-panels v-model="tab" animated>
          <q-tab-panel name="imageUpload">
            <div
              class="q-gutter-md row items-center"
              style="justify-content:center"
            >
              <q-file
                color="primary"
                v-model="fileUpload"
                :loading="loading.isLoading"
                dense
                outlined
                rounded
                counter
                use-chips
                max-file-size="1048576"
                :error="errorInput.isError"
                :error-message="errorInput.errorMessage"
                @rejected="onRejected"
                @click="resetErr"
                style="max-width: 500px; min-width: 600px"
                ref="uploadInput"
              >
                <template v-slot:prepend>
                  <q-icon name="image" />
                </template>

                <template v-slot:append>
                  <q-btn
                    :color="fileUpload ? 'primary' : 'grey'"
                    :disable="fileUpload ? false : true"
                    @click="submitImage"
                    round
                    dense
                    flat
                    icon="file_upload"
                  />
                </template>
              </q-file>
            </div>
            <div class="row" style="justify-content:center">
              <q-input
                v-model="textResult"
                counter
                standout
                readonly
                hide-bottom-space
                type="textarea"
                style="min-width: 600px"
              >
                <template v-slot:append>
                  <q-btn><q-icon name="content_copy" /></q-btn>
                </template>
              </q-input>
            </div>
          </q-tab-panel>

          <q-tab-panel name="urlUpload"> </q-tab-panel>
        </q-tab-panels>
      </q-card>
    </div>
  </div>
</template>

<script>
export default {
  name: "Home",
  components: {},
  data() {
    return {
      tab: "imageUpload",
      splitterModel: 20,
      fileUpload: null,
      loading: {
        isLoading: false,
        loadingColor: "primary",
      },
      errorInput: {
        isError: false,
        errorMessage: "",
      },
      textResult: null,
    };
  },
  mounted() {
    setTimeout(() => {
      this.$refs.uploadInput.focus();
    }, 1000);
  },
  methods: {
    submitImage() {
      this.loading.isLoading = true;
      const formData = new FormData();
      formData.append("file", this.fileUpload);
      const options = {
        method: "POST",
        headers: { apikey: "1f293d399788957" },
        body: formData,
      };
      console.log(options);

      fetch("https://api.ocr.space/parse/image/", options)
        .then((res) => {
          return res.json();
        })
        .then((result) => {
          let txtRusult = result.ParsedResults[0];
          console.log(result);
          console.log(result.ParsedResults[0].ParsedText);

          this.textResult = txtRusult.ParsedText;

          this.loading.isLoading = false;
        })
        .catch((err) => {
          console.log(222222, err);
          this.loading.isLoading = false;
        });
    },
    onRejected() {
      this.errorInput.errorMessage = "Maximum file size 1 MB";
      this.errorInput.isError = true;
    },
    resetErr() {
      this.errorInput.isError = false;
    },
  },
};
</script>
