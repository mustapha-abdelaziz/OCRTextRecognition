<template >
  <div class="q-pa-md fixed-center" >
    <div class="q-gutter-y-md ">
      <q-card class="shadow" style=" width:700px">
        <q-tabs
          v-model="tab"
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

        <q-tab-panels v-model="tab" animated style="">

          <!-- //////////////////////// -->
          <q-tab-panel name="imageUpload">
            <div class="row items-start justify-center q-mx-xs" style="margin: 1em 0">
              <q-file  style="width:70%"
                color="primary"
                v-model="fileUpload"
                :loading="loading.isLoading"
                dense
                outlined
                rounded
                counter
                use-chips
                accept=".jpg, .pdf, image/*"
                max-file-size="1048576"
                :error="errorInput.isError"
                :error-message="errorInput.errorMessage"
                @rejected="onRejected"
                @click="resetErr"
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


              <q-select class="q-mx-xs"
                outlined
                rounded
                menu-shrink
                v-model="lang"
                :options="options"
                label="Choose document language"
                dense
                style="margin-bottom:10px;width:25%;"
               
              />
            </div>

            <div v-if="true" class="row">
              <q-input class="q-ma-md"
                v-model="textResult"
                counter
                standout
                readonly
                autogrow
                type="textarea"
                style="width:100%; max-height:300px"
              >
                <template v-slot:after>
                  <q-btn round flat @click="copyToClipboard">
                    <q-icon name="content_copy" />
                  </q-btn>
                  <q-tooltip @hide="changeText">
                    {{ tooltipText }}
                  </q-tooltip>
                </template>
                
              </q-input>
            </div>
          </q-tab-panel>

          <!-- //////////////////////// -->

          <q-separator />

          <!-- Second Tab -->
          <q-tab-panel name="urlUpload">
            <div class="q-gutter-md row items-center" style="margin: 1em 0">
              <q-input
                color="primary"
                v-model="linkUrl"
                :loading="loading.isLoading"
                dense
                outlined
                rounded
                @click="resetErr"
                style="width: 600px; margin-right:10px"
                ref="uploadInput"
              >
                <template v-slot:prepend>
                  <q-icon name="insert_link" />
                </template>

                <template v-slot:append>
                  <q-btn
                    :color="linkUrl ? 'primary' : 'grey'"
                    :disable="linkUrl ? false : true"
                    @click="submitUrl"
                    round
                    dense
                    flat
                    icon="file_upload"
                  />
                </template>
              </q-input>
            </div>
            <div v-if="urlResult" class="row" style="justify-content:center">
              <q-input
                v-model="urlResult"
                counter
                standout
                readonly
                autogrow
                type="textarea"
                style="min-width: 600px"
              >
                <template v-slot:append>
                  <q-btn flat @click="copyToClipboard">
                    <q-icon name="content_copy" />
                  </q-btn>
                  <q-tooltip @hide="changeText">
                    {{ tooltipText }}
                  </q-tooltip>
                </template>
              </q-input>
            </div>
          </q-tab-panel>
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
      options: [
        {
          label: "Arabic",
          value: "ara",
        },
        {
          label: "Bulgarian",
          value: "bul",
        },
        {
          label: "Chinese(Simplified)",
          value: "chs",
        },
        {
          label: "Chinese(Traditional)",
          value: "cht",
        },
        {
          label: "Croatian",
          value: "hrv",
        },
        {
          label: "Czech",
          value: "cze",
        },
        {
          label: "Danish",
          value: "dan",
        },
        {
          label: "Dutch",
          value: "dut",
        },
        {
          label: "English",
          value: "eng",
        },
        {
          label: "Finnish",
          value: "fin",
        },
        {
          label: "French",
          value: "fre",
        },
        {
          label: "German",
          value: "ger",
        },
        {
          label: "Greek",
          value: "gre",
        },
        {
          label: "Hungarian",
          value: "hun",
        },
        {
          label: "Korean",
          value: "kor",
        },
        {
          label: "Italian",
          value: "ita",
        },
        {
          label: "Japanese",
          value: "jpn",
        },
        {
          label: "Polish",
          value: "pol",
        },
        {
          label: "Portuguese",
          value: "por",
        },
        {
          label: "Russian",
          value: "rus",
        },
        {
          label: "Slovenian",
          value: "slv",
        },
        {
          label: "Spanish",
          value: "spa",
        },
        {
          label: "Swedish",
          value: "swe",
        },
        {
          label: "Turkish",
          value: "tur",
        },
      ],
      lang: {
        label: "English",
        value: "eng",
      },
      tab: "imageUpload",
      fileUpload: null,
      linkUrl: null,
      urlResult: null,
      loading: {
        isLoading: false,
        loadingColor: "primary",
      },
      errorInput: {
        isError: false,
        errorMessage: "",
      },
      textResult: null,
      showTooltip: false,
      tooltipText: "Copy to Clipboard",
    };
  },
  mounted() {
    setTimeout(() => {
      this.$refs.uploadInput.focus();
    }, 1000);
  },
  methods: {
    changeText() {
      this.tooltipText = "Copy to Clipboard";
    },
    testLog() {
      console.log(this.lang);
      console.log("clicked");
    },
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

    ///////////////////////////////////
    submitUrl() {
      this.loading.isLoading = true;
      const Url =
        "https://api.ocr.space/parse/imageurl?apikey=1f293d399788957&url=" +
        this.linkUrl +
        "&language=" +
        this.lang.value;

      fetch(Url)
        .then((res) => {
          return res.json();
        })
        .then((result) => {
          let txtRusult = result.ParsedResults[0];
          this.urlResult = txtRusult.ParsedText;
          console.log(result);
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
    copyToClipboard() {
      navigator.clipboard.writeText(this.textResult);
      this.tooltipText = "Copied";
    },
  },
};
</script>


