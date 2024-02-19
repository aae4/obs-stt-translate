<template>
  <v-form v-model="valid">
    <v-container>
      <v-row justify="center">
        <v-col class="v-col-6">
          <div class="text-h5 text-grey">Speech & subs settings</div>
          <hr>
        </v-col>
      </v-row>

      <v-row justify="center">
        <v-col class="v-col-6">
          <v-text-field
            v-model="homeModel.lang"
            @input="event => homeModel.lang = event.target.value"
            disabled
            label="Audio language"
            required
            hide-details
          ></v-text-field>
        </v-col>
      </v-row>

      <v-row justify="center">
        <v-col class="v-col-6">
          <v-text-field
            v-model="subs_lang"
            disabled
            label="Subtatles language"
            required
            hide-details
          ></v-text-field>
        </v-col>
      </v-row>

      <v-row justify="center">
        <v-col class="v-col-6">
          <div class="text-h5 text-grey">OBS Websocket</div>
          <!-- <v-col></v-col> -->
          <!-- <v-divider inset></v-divider> -->
          <hr>
        </v-col>
      </v-row>

      <v-row justify="center">
        <v-col class="v-col-6">
          <v-text-field
            v-model="homeModel.websocketAddress"
            @input="event => homeModel.websocketAddress = event.target.value"
            :disabled="isRunning"
            prefix="ws://"
            :rules="requried"
            label="Address"
            required
            hide-details
          ></v-text-field>
        </v-col>
      </v-row>

      <v-row justify="center">
        <v-col class="v-col-6">
          <v-text-field
            v-model="homeModel.websocketPassword"
            @input="event => homeModel.websocketPassword = event.target.value"
            :disabled="isRunning"
            :append-icon="show1 ? 'mdi-eye' : 'mdi-eye-off'"
            :type="show1 ? 'text' : 'password'"
            label="Password"
            counter
            @click:append="show1 = !show1"
          ></v-text-field>
        </v-col>
      </v-row>

      <v-row justify="center">
        <v-col class="v-col-6">
          <div class="text-h5 text-grey">OBS Text Source</div>
          <hr>
        </v-col>
      </v-row>

      <v-row justify="center">
        <v-col
          cols="6"
          md="3"
        >
          <v-text-field
            v-model="homeModel.sourceName"
            @input="event => homeModel.sourceName = event.target.value"
            :disabled="isRunning"
            :rules="requried"
            :counter="10"
            label="Source name"
            hide-details
            required
          ></v-text-field>
        </v-col>
        <v-col
          cols="6"
          md="3"
        >
          <v-select
            v-model="homeModel.sourceType"
            @input="event => homeModel.sourceType = event.target.value"
            :disabled="isRunning"
            :items="source_type_items"
            label="Source type"
          ></v-select>
        </v-col>
      </v-row>

      <v-row justify="center">
        <v-col
          cols="6"
          md="3"
        >
          <v-text-field
            v-model="homeModel.maxRows"
            @input="event => homeModel.maxRows = event.target.value"
            :disabled="isRunning"
            :rules="requried"
            :counter="10"
            label="Number of lines"
            hide-details
            required
          ></v-text-field>
        </v-col>
        <v-col
          cols="6"
          md="3"
        >
          <v-text-field
            v-model="homeModel.maxCols"
            @input="event => homeModel.maxCols = event.target.value"
            :disabled="isRunning"
            :rules="requried"
            :counter="10"
            label="Characters per line"
            hide-details
            required
          ></v-text-field>
        </v-col>
      </v-row>

      <v-row justify="center" v-if="errorMessage">
        <v-col class="v-col-6">
          <v-alert :text="errorMessage" type="error" variant="tonal"></v-alert>
        </v-col>
      </v-row>

<!--     <div class="row">
      <div class="col-md-12">
        <div class="alert alert-danger" role="alert" if="{ errorMessage }">
          <strong>Error</strong>: { errorMessage }
        </div>
      </div>
    </div> -->

      <v-row justify="center">
        <v-col class="v-col-6" align="end">
          <v-btn
            :color="isRunning ? 'error' : ''"
            block
            @click="onToggleStartStopButton"
          >
            {{ isRunning ? 'Stop' : 'Run' }}
          </v-btn>
        </v-col>
      </v-row>
    </v-container>
  </v-form>
</template>
<script>
  import HomeModel from "../models/HomeModel.js";

  export default {
    data: () => ({
      errorMessage: '',
      homeModel: new HomeModel(),
      isRunning: false,
      valid: false,
      // audio_lang: 'ru-RU',
      subs_lang: 'en',
      // ws_address: 'localhost:4444',
      // password: '12345678',
      show1: true,
      // lastname: '',
      // source_name: '',
      // source_type: 0,
      source_type_items: [
        {title: 'Text (GDI+) [Windows]', value: 0},
        {title: 'Text (FreeType 2) [Mac/Linux]', value: 1}
      ],
      // num_lines: '2',
      // line_length: '50',
      requried: [
        value => {
          if (value) return true

          return 'Is required.'
        },
      ],
      // nameRules: [
      //   value => {
      //     if (value) return true

      //     return 'Name is required.'
      //   },
      //   value => {
      //     if (value?.length <= 10) return true

      //     return 'Name must be less than 10 characters.'
      //   },
      // ],
      // email: '',
      // emailRules: [
      //   value => {
      //     if (value) return true

      //     return 'E-mail is requred.'
      //   },
      //   value => {
      //     if (/.+@.+\..+/.test(value)) return true

      //     return 'E-mail must be valid.'
      //   },
      // ],
    }),
    methods: {
      onToggleStartStopButton: function (event) {
       if (this.isRunning) {
         this.homeModel.stop();
         this.isRunning = false;
         // this.update();
       } else {
         this.homeModel
             .start()
             .then(() => {
               this.isRunning = true;
               // this.update();
             })
             .catch(err => {
               console.log(err);
               this.errorMessage = err.error;
               // this.update();
             });
       }
      }
    },
    // beforeCreate: function() {
    //   this.homeModel = new HomeModel();
    // },
    // created: function() {
    //   this.source_type = homeModel.sourceType;
    //   this.audio_lang = homeModel.lang;
    // }
  }
</script>


<!--   <script>


   export default {
     isRunning: false,
     errorMessage: null,
     homeModel: new HomeModel(),
     onMounted() {
       if (!this.homeModel.isSupported) {
         this.errorMessage = "Speech recognition is not supported on this browser. Use Google Chrome 33+ or Edge 79+ please.";
         this.update();
       }
     },
     onToggleStartStopButton() {
       this.errorMessage = null;

       if (this.isRunning) {
         this.homeModel.stop();
         this.isRunning = false;
         this.update();
       } else {
         this.homeModel
             .start()
             .then(() => {
               this.isRunning = true;
               this.update();
             })
             .catch(err => {
               console.log(err);
               this.errorMessage = err.error;
               this.update();
             });
       }
     }
   };
  </script> -->
