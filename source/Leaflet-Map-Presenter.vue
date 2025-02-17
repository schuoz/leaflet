  { 
  template: `
        <!-- This template use https://bootstrap-vue.js.org/ -->

        <b-row v-if="pybossa.userProgressInPercent < 100">

        <b-row  class="w-100" align-h="start">

          <!-- How-To button -->
          <b-button @click="openModal(0)"
          variant="light" 
          class="m-1" size="sm"
          style="max-height:1.5rem;">
              {{ text.howto_btn }}
          </b-button>

          <!-- Disclaimer button -->
          <b-button @click="openModal(1)" 
          variant="light"  
          class="m-1"  size="sm"
          style="max-height:1.5rem;">
              {{ text.disclaimer_btn }}
          </b-button>
      </b-row>




        <!-- Form zone -->
        <b-col md="5" class="mt-4 mt-md-0 order-2 order-md-1">
          <!-- Questions with answers -->
          <b-form-group
            :key="question.id"
            v-for="question in questionList"
            label-size="lg"
            class="mt-2 mb-4"
          >
            <label>
              {{question.question}}
              <b-icon v-if="question.info"  v-b-popover.hover.html="createPopover(question.info)" icon="info-circle" variant="info"></b-icon>
              <span v-if="question.required" class="text-primary font-weight-bold h5">
                *
              </span>
            </label>
            <common-editor-elements
              :answers="answers"
              :question="question"
              :context="context"
            />
          </b-form-group>

          <!-- Submit button -->
          <b-btn @click="submit" variant="success" class="mt-3"
            >{{$t('submit-btn')}}
          </b-btn>

          <!-- Skip button -->
          <b-btn @click="skip" variant="secondary" class="mt-3"
            >{{$t('skip-btn')}}
          </b-btn>

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

        <b-col md="7" class="order-1 order-md-2">
          <div
            class="text-center"
            style="position: sticky; top: 15%"
          >
          <!-- Map -->
          <maps
            class="my-2"
            :resetToInitial="false"
            :hide-icons="true"
            :scroll-to-zoom="false"
            :hide-interaction="true"
            :hide-geo-search="true"
            :set-rectangle="true"
            :polygon="taskPolygon"
            :rectangle="rectangle"
            :max-bounds="rectangle.bounds"
            style="height: 700px; width: 700px; "
            :mapSettings="mapSettings"
            :taskLoaded="pybossa.taskLoaded"
          ></maps>
          </div>
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

      <!-- Task end message -->
      <b-row v-else>
        <b-col>
          <b-jumbotron
            :header="$t('template-editor-text-6')"
            :lead="$t('template-editor-text-7')"
          ></b-jumbotron>
        </b-col>
      </b-row>`,

    data: () => { return {
      questions:
        [
          {
            "id": 1,
            "question": "On the side of the river, what artificial objects do you see?",
            "info": [{term:'farms', explanation:'very interesting content'}, {term:'planted forests', explanation:'very interesting content'}],
            "answers": [
              "small buildings",
              "large buildings",
              "agriculture fields",
              "farms",
              "planted forests",
              "industrial facilities",
              "small roads",
              "large roads",
              "other artificial objects"
            ],
            "type": "one_choice",
            "required": false,
            "isDependent": false,
            "condition": {}
          },
          {
            "id": 2,
            "question": "In or over the river, what artificial objects do you find?",
            "answers": [
              "dam",
              "small harbor",
              "large harbor",
              "small bridge",
              "large bridge",
              "small ships",
              "large ships",
              "river mining",
              "other artificial objects"
            ],
            "type": "multiple_choice",
            "required": false,
            "isDependent": false,
            "condition": {}
          },
          {
            "id": 3,
            "question": "On the sides of the river, what natural objects do you see?",
            "answers": [
              "wetlands",
              "natural forest",
              "natural non-forest vegetation",
              "natural bare land",
              "other natural landscape"
            ],
            "type": "multiple_choice",
            "required": false,
            "isDependent": false,
            "condition": {}
          },
          {
            "id": 4,
            "question": "In the river, what natural objects do you see?",
            "answers": [
              "patches of sands",
              "small island structures",
              "large islands structures",
              "floating vegetations",
              "other form of natural objects"
            ],
            "type": "multiple_choice",
            "required": false,
            "isDependent": false,
            "condition": {}
          },
          {
            "id": 5,
            "question": "What river shape would you describe this river section?",
            "answers": [
              "artificial linear",
              "natural linear",
              "fish skeleton shape",
              "braided",
              "alluvial",
              "S shape",
              "Z shape",
              "other shape"
            ],
            "type": "multiple_choice",
            "required": false,
            "isDependent": false,
            "condition": {}
          },
          {
            "id": 6,
            "question": "How many river channels do you see?",
            "answers": "",
            "type": "short_answer",
            "required": false,
            "isDependent": false,
            "condition": {}
          },
          {
            "id": 6,
            "question": "Do you see a huge wetland surrounding the river?",
            "answers": ["Yes", "No"],
            "type": "dropdown",
            "required": false,
            "isDependent": false,
            "condition": {}
          }
        ],
      visible: false,
      modalShow: true,
      tabIndex: 0,
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

      createPopover: function(info){
        var html = '';
        for (var i = 0; i < info.length; i++){
          html += '<strong>' + info[i].term + ':</strong> ' + info[i].explanation + '<br>';
        }
        return html;
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
        context: function(){return this},
        taskGeo: function(){return JSON.parse(this.taskInfo[".geo"])["coordinates"]},
        taskPolygon: function(){return JSON.parse(this.taskInfo["bounds"])["coordinates"]},
        makeRectangle: function(){return [[this.taskPolygon[0][1], this.taskPolygon[0][0]],[ this.taskPolygon[2][1], this.taskPolygon[2][0] ] ]},
        mapSettings: function(){return {
          zoom: this.zoom,
          center: [this.taskGeo[1],this.taskGeo[0]],
          maxMarkers: 0,
          markers: false,
          area: true,
          mapType: "Aerial",
          static_map: true
        }},
        rectangle: function(){return {
          bounds: this.makeRectangle,
          style: { color: 'black', weight: 5 , fill: false}
        }},
        center: function(){return [this.taskGeo[1],this.taskGeo[0]]}
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
