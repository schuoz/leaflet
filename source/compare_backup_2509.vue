  { 
      template: `
            <!-- This template use https://bootstrap-vue.js.org/ -->
            <div v-if="pybossa.userProgressInPercent < 100"">
            <b-row>
            <!-- right columns - Media -->
            <b-col xl="6" lg="12" md="12" sm="12">
            <div @click="submitAnswer('A')" >
              <maps
                class="my-2 border border-5"
                :resetToInitial="false"
                :hide-icons="true"
                :scroll-to-zoom="false"
                :hide-interaction="true"
                :hide-geo-search="true"
                style="height: 700px; width: 700px; "
                :mapSettings="mapSettingsA"
                :taskLoaded="pybossa.taskLoaded"
              ></maps>
              </div>
            </b-col>
            <b-col  xl="6" lg="12" md="12" sm="12">
              <div @click="submitAnswer('B')">

              <maps
                class="my-2"
                :resetToInitial="false"
                :hide-icons="true"
                :scroll-to-zoom="false"
                :hide-interaction="true"
                :hide-geo-search="true"
                style="height: 700px; width: 700px; "
                :mapSettings="mapSettingsB"
                :taskLoaded="pybossa.taskLoaded"
              ></maps>
              </div>
            </b-col>
          </b-row>
          <b-row align-h="center">
          <!-- Form zone -->
            <b-col xl="6" lg="8" md="8" sm="12" class="d-flex justify-content-center text-center" >

          <!-- Left button -->
          <b-button size="lg" @click="submitAnswer('A')" variant="success" class="m-3"
            >Left
          </b-button>
          
          <!-- Right button -->
          <b-button size="lg" @click="submitAnswer('B')" variant="success" class="m-3"
            > Right
          </b-button>

          </b-row>

          <b-row align-h="center">
          <!-- Form zone -->
            <b-col xl="6" lg="8" md="8" sm="12" class="justify-content-center text-center">
            <!-- Form validation errors -->
            <b-alert variant="danger" v-model="showAlert" class="mt-2" dismissible>
              {{$t('template-editor-text-8')}}
            </b-alert>
  
            <!-- User progress -->
            <!-- <p class="mt-2">You are working now on task: <b-badge variant="warning">{{ task.id }}</b-badge></p>-->
            <p class="mt-2">
              {{$t('template-editor-text-2')}}:
              <b-badge variant="primary">{{ pybossa.userProgress.done }}</b-badge>
              {{$t('template-editor-text-2a')}}
              <b-badge variant="primary">{{ pybossa.userProgress.total }}</b-badge>
              {{$t('template-editor-text-3')}}
            </p>
  
              <b-progress :value="pybossa.userProgressInPercent" :max="100"></b-progress>
            </b-col>


            <!-- Modal for survey -->
        <b-modal 
          v-model="modalShow"
          id="bv-modal-presurvey" 
          scrollable
          size="xl">
          <template #modal-header> <h3>{{ text.soc_greeting }}</h3> </template>
          <b-card no-body class="border-0" style="max-height:65vh;">
            <b-tabs v-model="tabIndex" card>
                <b-tab :title="text.howto_btn">
                  <b-card-text> 
                      <p>{{ text.howto_summary }}</p>
                  </b-card-text> 
                    <b-embed
                      type="iframe"
                      :src="text.howto_video_url" 
                      :title="text.howto_video_title" 
                      aspect="16by9"
                      frameborder="0" 
                      allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" 
                      allowfullscreen></iframe>
                    ></b-embed>
                    <b-card-text class="mt-3"> 
                      <h5>{{ text.howto_title }}</h5>
                      <p>{{ text.howto_para01 }}</p>
                      <p>{{ text.howto_para02 }}</p>
                      <p>{{ text.howto_para03 }}</p>
                      <p>
                        {{ text.howto_link_description }}
                        <a :href="text.howto_link_url" target=”_blank”>{{ text.howto_link_text }}</a>
                      </p>
                      <p>{{ text.howto_para04 }}</p>
                      <p>
                        {{ text.howto_para05 }}
                        <strong>{{ text.howto_para05_bold }}</strong>
                      </p>
                  </b-card-text> 
                </b-tab>
                <b-tab :title="text.disclaimer_btn">
                    <b-card-text>
                          <h4>{{ text.soc_optional_title }}</h4>
                          <p>{{ text.soc_optional_p1 }}</p>
                          <p>{{ text.soc_optional_p2 }}</p>
                    </b-card-text>
                    <b-card-text>
                          <h4>{{ text.soc_withdraw_title }}</h4>
                          <p>{{ text.soc_withdraw_p1 }}</p>
                    </b-card-text>
                    <b-card-text>
                      <h4>{{ text.soc_consent_title }}</h4>
                          {{ text.soc_consent_list_header }}
                          <ul style="list-style-type: disc; margin-left: 30px">
                            <li>{{ text.soc_consent_list_1 }}</li>
                            <li>{{ text.soc_consent_list_2 }}</li>
                            <li>{{ text.soc_consent_list_3 }}</li>
                            <li>{{ text.soc_consent_list_4 }}</li>
                          </ul>
                    </b-card-text>
                </b-tab>
              </b-tabs>
          </b-card>

          <template #modal-footer>
                <b-button variant="light" @click="decTabIndex" :disabled="tabIndex < 1" >
                  {{ text.soc_back }}
                </b-button>
                <b-button variant="success" @click="incTabIndex" v-if="tabIndex < 1">
                  {{ text.soc_continue }}
                </b-button>
                <b-button variant="success" @click="closeModal()" v-if="tabIndex > 0">
                  {{ text.soc_skip}}
                </b-button>
          </template>
        </b-modal>
          </b-row>  


          </div>
          <!-- Task end message -->
          <div v-else>
          <b-row>
            <b-col>
              <b-jumbotron
                :header="$t('template-editor-text-6')"
                :lead="$t('template-editor-text-7')"
              ></b-jumbotron>
            </b-col>
          </b-row>
          </div>
          
      
          `,
      
        data: () => { return {
          visible: false,
          modalShow: true,
          tabIndex: 0,
          questions:[],
          markedPlaces:[],
          area:{"latlngs":[]},
          answers: [],
          zoom: 15,
          showAlert: false,
          questionList: [],
          translations: {
            "de": {
              soc_skip: "Start",
              soc_continue: "Continue",
              soc_back: "Back",
              soc_greeting: "Stop Hate Speech",
              soc_withdraw_p1: "Ihre Teilnahme an der Studie ist freiwillig. Sie können jederzeit und ohne einen Grund zu nennen von der Studie zurücktreten. Klicken Sie dafür auf  «Zurücktreten» (wird oben links im Fenster angezeigt). Wenn Sie von der Studie zurücktreten, werden alle Ihre Antworten unwiderruflich gelöscht.",
              soc_consent_title: "Einverständniserklärung",
              soc_consent_list_header: "Mit der Teilnahme an der Studie bestätige ich, dass ich:",
              soc_consent_list_1: "die Studieninformationen gehört/gelesen und verstanden habe.",
              soc_consent_list_2: "genügend Zeit hatte, mich für meine Teilnahme an der Studie zu entscheiden.",
              soc_consent_list_3: "freiwillig an der Studie teilnehme und damit einverstanden bin, dass meine persönlichen Daten wie unten beschrieben verwendet werden.",
              soc_consent_list_4: "jederzeit und ohne Begründung von der Studie zurücktreten kann.",
              howto_title: "How-To",
              howto_video_url: "https://www.youtube-nocookie.com/embed/BLMrBT4-gog",
              howto_video_title: "How-To Video",
              howto_summary: "Unterstützen Sie uns dabei, die algorithmische Erkennung von Hate Speech zu verbessern und Hate Speech zu reduzieren!",
              howto_para01: "Lesen Sie Kommentare zu Artikeln aus dem Internet und entscheiden Sie, ob es sich um Hate Speech respektive toxischen Sprachgebrauch handelt oder nicht.",
              howto_para02: "Unter ‘Hate Speech’ verstehen wir abwertenden/beleidigenden/vulgären Sprachgebrauch, der sich gegen bestimmte Personengruppen und aufgrund gewisser Identitätsmerkmale richtet. Geben Sie in Fällen von Hate Speech daher bitte auch an, gegen welche Personengruppe der Kommentar gerichtet ist.",
              howto_para03: "'Toxischer' Sprachgebrauch richtet sich nicht gegen Personen-Gruppen, ist aber abwertend/beleidigend/vulgär ist.  In dem Fall beantworten Sie die Frage nach Hate Speech oder toxischem Sprachgebrauch ebenfalls mit Ja, geben dann aber im nächsten Schritt keine Personengruppe an, gegen die sich der Kommentar richtet.",
              howto_para04: "Damit Sie den Kontext kennen, können Sie zu jedem Kommentar den vollständigen Zeitungsartikel lesen. Für das Verständnis der meisten Kommentare ist dies aber nicht notwendig.",
              howto_para05: "Wenn Sie 50 oder mehr Kommentare klassifiziert haben und registriert sind, nehmen Sie automatisch an der",
              howto_para05_bold: "Preisverlosung für einen von drei Digitec Gutscheinen über 50.- CHF teil.",
              howto_link_description: "Lesen Sie hier unsere",
              howto_link_url: "https://stophatespeech.ch/pages/definition-von-hate-speech",
              howto_link_text: "Hate Speech Definition",
              submit_btn: "Next",
              skip_btn: "Skip",
              howto_btn: "How-To",
              disclaimer_btn: "Disclaimer",
            },
          }
        }},
      
        methods: {

          submitAnswer: function(answer){
              this.answers = answer;
              this.submit();
          },
          decTabIndex: function(){
            if (this.tabIndex > 0) {this.tabIndex--;}
          },
          incTabIndex: function(){
            if (this.tabIndex < 2) {this.tabIndex++;}
          },
          openModal: function(tabIndex = 0){
            this.tabIndex = tabIndex;
            this.modalShow = true;
          },
          closeModal: function(){
            this.modalShow = false;
          },
          submit: function(){this.isFormValid()?(this.pybossa.saveTask(this.answers),this.initialize()):this.showAlert=!0},
          skip: function(){this.pybossa.skip(),this.initialize()},
          isFormValid: function(){var t=this,e=!0;return this.questionList.every(function(s){var i=t.answers.find(function(t){return t.qid==s.id})||[];return!(s.required&&(!i.value||i.value.length<=0))||(e=!1,!1)}),e},
          initialize: function(){this.showAlert=!1;var t=this.pybossa;this.questionList=this.questions.filter(function(e){return t.isConditionEmpty(e)}),this.answers=this.questions.map(function(t){var e={qid:t.id,question:t.question,value:null};return"multiple_choice"===t.type&&(e.value=[]),e}),window.scrollTo({top:0,behavior:"smooth"})},
          },
        
      
          computed: {
            
            task: function(){return this.pybossa.task},
            taskInfo: function(){return this.task.info},
            text: function(){return this.translations.de},
            taskGeoA: function(){return JSON.parse(this.taskInfo["geo_A"])["coordinates"]},
            taskGeoB: function(){return JSON.parse(this.taskInfo["geo_B"])["coordinates"]},
            mapSettingsA: function(){return {
              zoom: this.zoom,
              center: [this.taskGeoA[1],this.taskGeoA[0]],
              maxMarkers: 0,
              markers: false,
              area: true,
              mapType: "Aerial",
              static_map: true
            }},
            mapSettingsB: function(){return {
              zoom: this.zoom,
              center: [this.taskGeoB[1],this.taskGeoB[0]],
              maxMarkers: 0,
              markers: false,
              area: true,
              mapType: "Aerial",
              static_map: true
            }},
            context: function(){return this}
            },
      
        created() {
          this.initialize();
        },
      
        mounted() {
          this.pybossa.run();
        },
      
        watch: {
          /* Watch no nested elements */
        },
      
        props: {
          /* Injected by the Pybossa App */
          pybossa: {
            required: true,
          },
        }
      }
