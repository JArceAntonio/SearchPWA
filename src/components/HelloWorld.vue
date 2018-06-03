<template>

  <v-card flat>
    <v-toolbar dark color="primary" flat extended>

    </v-toolbar>
    <v-layout row pb-2>
      <v-flex xs10 sm8 offset-sm2 offset-xs1>
        <v-card class="card--flex-toolbar">
          <v-toolbar card prominent>
            <v-toolbar-title class="body-3">Buscador Inteligente</v-toolbar-title>
              <v-spacer></v-spacer>
              <v-btn icon @click="dialogTextSearch = true">
                <v-icon>search</v-icon>
              </v-btn>
              <v-btn icon @click="dialogImageSearch = true">
                <v-icon>image</v-icon>
              </v-btn>
              <v-btn icon @click="startSpeechRecognition">
                <v-icon>mic</v-icon>
              </v-btn>
            <!--<v-spacer></v-spacer>-->

          </v-toolbar>

          <v-layout>
            <v-container>
              <div v-if="textSearch.length > 0">
                Texto de busqueda
                <v-chip >{{textSearch}}</v-chip>
              </div>
              <div v-if="urlImage.length > 0">
                <p>Imagen de busqueda</p>
                <p><img :src="urlImage" height="150px"></p>

              </div>
              <v-container grid-list-sm>
                <v-layout v-if="textSearch.length > 0 && dataText !== null" row wrap>

                  <v-flex xs12 sm6 md4 lg3 xl2 v-for="(imageData, index) in dataText.images.value" :key="index">
                    <v-card height="400px" style="overflow: hidden" class="elevation-5">
                      <img :src="imageData.thumbnailUrl" height="150px" />
                      <v-card-title style="height: 180px; overflow: hidden">
                        <div class="mt-2">{{imageData.name}}</div>
                      </v-card-title>
                      <v-card-actions class="mb-0">
                        <v-btn flat color="orange" @click="openUrl(imageData.hostPageUrl)">Ver Mas</v-btn>
                      </v-card-actions>
                    </v-card>
                  </v-flex>

                  <v-flex xs12 v-for="(textData, index) in dataText.webPages.value" :key="index">
                    <v-card class="elevation-5">
                      <v-card-title primary-title>
                        <div>
                          <h3 class="headline mb-0">{{textData.name}}</h3>
                          <div>{{textData.snippet}}</div>
                        </div>
                      </v-card-title>

                      <v-card-actions class="mb-0">
                        <v-btn flat color="orange" @click="openUrl(textData.displayUrl)">Ver Mas</v-btn>
                      </v-card-actions>
                    </v-card>
                  </v-flex>

                </v-layout>
                <v-layout v-if="urlImage.length > 0" row wrap>
                  <v-flex xs12 sm6 md4 lg3 xl2 v-for="(image, index) in dataImage" :key="index">
                    <v-card height="400px" class="elevation-5">
                      <img :src="image.thumbnailUrl" height="150px"/>
                      <v-card-title style="height: 180px; overflow: hidden">
                          <div class="mt-2">{{image.name}}</div>
                      </v-card-title>
                      <v-card-actions class="mb-0">
                        <v-btn flat color="orange" @click="openUrl(image.hostPageUrl)">Ver Mas</v-btn>
                      </v-card-actions>
                    </v-card>
                  </v-flex>
                </v-layout>
              </v-container>

            </v-container>
          </v-layout>
        </v-card>
      </v-flex>
    </v-layout>

    <!--BUSQUEDA POR TEXTO-->
    <v-dialog v-model="dialogTextSearch" :max-width="isMobile ? '80%' : '40%'">
      <v-card>
        <v-card-title>
          <h2>Busqueda por texto</h2>
        </v-card-title>
        <v-layout>
          <!--AQUI VA EL CONTENIDO-->
          <v-container>
            <v-flex>
              <v-text-field
                placeholder="Ingrese el criterio de busqueda"
                v-model="textSearch"
              >
              </v-text-field>
              <v-select
                :items="items"
                v-model="e1"
                label="Select"
                single-line
              ></v-select>
            </v-flex>
          </v-container>

        </v-layout>
        <v-card-actions>
          <v-btn color="red" flat @click.stop="dialogTextSearch=false">Cerrar</v-btn>
          <v-btn color="indigo" flat @click.stop="performTextSearch()">Buscar</v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>

    <!--BUSQUEDA POR IMAGENES-->
    <v-dialog v-model="dialogImageSearch" :max-width="isMobile ? '80%' : '40%'">
      <v-card>
        <v-card-title>
          <h2>Busqueda por imagen</h2>
        </v-card-title>
        <v-layout>

          <v-container mt2 mb2>
            <v-flex sm8 offset-sm2 xs10 offset-xs1>
              <div>
                <img id="frame" height="250" v-show="urlImage.length > 0" :src="urlImage">
              </div>
              <input type="file" accept="image/*" capture="camera" id="camera" @change="upload">
              <v-select
                :items="items"
                v-model="e1"
                label="Select"
                single-line
              ></v-select>
            </v-flex>
          </v-container>
        </v-layout>
        <v-card-actions>
          <v-btn color="red" flat @click.stop="dialogImageSearch=false">Cerrar</v-btn>
          <v-btn color="indigo" flat @click="performVisualSearch">Buscar</v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>

    <!--BUSQUEDA POR VOZ-->
    <v-dialog v-model="dialogVoiceSearch" :max-width="isMobile ? '80%' : '40%'" persistent>
      <v-card>
        <v-card-title>
          <h2>
            Busqueda por voz
          </h2>
        </v-card-title>
        <v-layout>
          <!--AQUI VA EL CONTENIDO-->
          <v-container>
            <h3 class="text-md-center">
              Escuchando...
            </h3>
            <div>
              {{ textSearch }}
            </div>
            <v-select
              :items="items"
              v-model="e1"
              label="Select"
              single-line
            ></v-select>
          </v-container>
        </v-layout>
        <v-card-actions>
          <v-btn color="indigo" @click="stopSpeechRecognition" flat >Buscar</v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
  </v-card>

</template>


<script>
  import config from './Config'
  import axios from 'axios'
  export default {
    data(){
      return{
        show: false,
        dialogTextSearch: false,
        dialogImageSearch: false,
        dialogVoiceSearch: false,
        textSearch: '',
        urlImage:'',
        capturedText: '',
        file: null,
        recognition: null,
        e1:'Argentina',
        items: ['Argentina','Chile', 'Brazil','Mexico', 'Estados Unidos'],
        isMobile: false,
        dataImage: [],
        dataText: null
      }
    },
    created(){
      try {
        var SpeechRecognition = window.SpeechRecognition || window.webkitSpeechRecognition;
        this.recognition = new SpeechRecognition();
        this.recognition.onresult = (event) => {
          var current = event.resultIndex;
          // Get a transcript of what was said.
          var transcript = event.results[current][0].transcript;
          var mobileRepeatBug = (current == 1 && transcript == event.results[0][0].transcript);
          if(!mobileRepeatBug) {
            this.textSearch += event.results[current][0].transcript
          }
        }
        this.recognition.onstart = () => this.textSearch = ''
      }
      catch(e) {
        console.error(e);
      }
      var isMobile = {
        Android: function() {
          return navigator.userAgent.match(/Android/i);
        },
        BlackBerry: function() {
          return navigator.userAgent.match(/BlackBerry/i);
        },
        iOS: function() {
          return navigator.userAgent.match(/iPhone|iPad|iPod/i);
        },
        Opera: function() {
          return navigator.userAgent.match(/Opera Mini/i);
        },
        Windows: function() {
          return navigator.userAgent.match(/IEMobile/i);
        },
        any: function() {
          return (isMobile.Android() || isMobile.BlackBerry() || isMobile.iOS() || isMobile.Opera() || isMobile.Windows());
        }
      }
      this.isMobile = isMobile.any()

    },
    methods:{
      upload(e){
        const file = e.target.files[0]
        this.file = file
        this.urlImage = URL.createObjectURL(file)
      },
      performVisualSearch(){
        this.textSearch = ''
        this.dataImage.splice(0, this.dataImage.length)
        let form = new FormData()
        form.append('image',this.file,this.file.name)
        axios.post(
          config.searchCredentials.urlVisual + '?mkt=' + this.getMarket(),
          form,
          {
            maxContentLength: 2000*1024,
            headers: {
              'Ocp-Apim-Subscription-Key' : config.searchCredentials.key,
              'Content-Type': 'multipart/form-data'
            }
          })
          .then((response) => {

            response.data.tags.forEach((tagActions) => {
              tagActions.actions.forEach((action)=>{
                if (action.actionType && (action.actionType === 'ShoppingSources' || action.actionType === 'ProductVisualSearch' || action.actionType === 'VisualSearch')) {
                  action.data.value.forEach((dataValue) => {
                    this.dataImage.push(dataValue)
                  })
                }
              })
            })

          })
          .catch(error => console.log(error))
      },
      performTextSearch(){
        this.dialogTextSearch = false
        this.dialogVoiceSearch = false
        this.urlImage = ''
        if(this.textSearch.trim().length > 0){
          axios.get(config.searchCredentials.urlWeb + '?q=' + this.textSearch + '&mkt=' + this.getMarket(),{
            headers:{ 'Ocp-Apim-Subscription-Key' : config.searchCredentials.key }
          }).then( response => {
            this.dataText = {...response.data}
          })
            .catch(error => console.log(error))
        }

      },
      startSpeechRecognition(){
        this.dialogVoiceSearch = true
        this.recognition.start()
      },
      stopSpeechRecognition(){
        this.dialogVoiceSearch = false
        this.recognition.stop()
        this.performTextSearch()
      },
      getMarket(){
        switch (this.e1){
          case  'Argentina':
            return 'es-ar'
          case  'Chile':
            return 'es-cl'
          case  'Brazil':
            return 'pt-br'
          case  'Mexico':
            return 'es-mx'
          case  'Estados Unidos':
            return 'es-us'
        }
      },
      openUrl(url){
        window.open(url)
      }
    },


  }
</script>


<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
  .card--flex-toolbar {
    margin-top: -64px;
  }

</style>
