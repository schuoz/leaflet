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
          "question": "What river shape would you describe this river section?",
          "info": [{term:'artificial linear', explanation:'straight linear and may have concrete river bank'}, {term:'natural linear', explanation:'Straight with a slight sinuosity'}, {term:'regular meandering', explanation:'with regular sinuous curves in the channel of a river'}, 
          {term:'irregular meandering', explanation:'with irregular sinuous curves in the channel of a river'}, {term:'braided', explanation:'a type of river that form a network of many branches within a channel'}, 
          {term:'alluvial', explanation:'is the river in which the bed and banks are made up of mobile sediment or soil'}],
          "answers": [
            "artificial linear",
            "natural linear",
            "regular meandering",
            "irregular meandering",
            "braided",
            "alluvial",
            "discontinuous",
            "other shape"
          ],
          "type": "multiple_choice",
          "required": false,
          "isDependent": false,
          "condition": {}
        },
        {
          "id": 2,
          "question": "In the river, what natural objects do you see?",
          "info": [{term:'patches of sands', explanation:'temporary sands or sediment bars'}, {term:'islands structures', explanation:'permanent or semi-permanent big natural islands'}],
          "answers": [
            "patches of sands",
            "islands structures",
            "floating vegetations",
            "other form of natural objects",
            "no natural objects"
          ],
          "type": "multiple_choice",
          "required": false,
          "isDependent": false,
          "condition": {}
        },
        {
            "id": 3,
            "question": "On the sides of the river, what natural objects do you see?",
            "info": [{term:'wetlands', explanation:'a place in which the land is covered by water—salt, fresh, or somewhere in between'}, {term:'natural non-forest vegetation', explanation:'depends on the climate, some areas can not forster forests, so there are some other type of natural vegetation'}],
            "answers": [
              "wetlands",
              "natural forest",
              "natural non-forest vegetation",
              "natural bare land",
              "other natural landscape",
              "no natural landscape"
            ],
            "type": "multiple_choice",
            "required": false,
            "isDependent": false,
            "condition": {}
        },
        {
            "id": 4,
            "question": "In or over the river, what artificial objects do you find?",
            "info": [{term:'mining', explanation:'extraction of valuable geological materials from the river channel or nearby '}, {term:'dam', explanation:'a barrier that stops or restricts the flow of surface water'}],
            "answers": [
              "dam",
              "harbor",
              "bridge",
              "cargo ship",
              "mining",
              "other artificial objects",
              "no artificial structure"
            ],
            "type": "multiple_choice",
            "required": false,
            "isDependent": false,
            "condition": {}
        },
        {
            "id": 5,
            "question": "On the side of the river, what artificial objects do you see?",
            "info": [{term:'planted forests', explanation:'cultivated forests with similar size and intervals'}, {term:'industrial facilities', explanation:'factory or other infrastructures near the river for cooling or easy-acess to water'}],
            "answers": [
              "buildings",
              "agriculture fields",
              "planted forests",
              "industrial facilities",
              "roads",
              "other artificial objects",
              "no artificial structure"
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
          soc_greeting: "Pritine river finder",
          soc_withdraw_p1: "Your participation in the study is voluntary. You can withdraw from the study at any time and without giving a reason. To do so, click on «Withdraw»  (displayed at the top left of the window). If you withdraw from the study, all your answers will be irrevocably deleted.",
          soc_consent_title: "Statement of consent",
          soc_consent_list_header: "By participating in the study, I confirm that I:",
          soc_consent_list_1: "have heard/read and understood the study information.",
          soc_consent_list_2: "had enough time to decide on my participation in the study.",
          soc_consent_list_3: "voluntarily participate in the study and agree to my personal data being used as described below.",
          soc_consent_list_4: "may withdraw from the study at any time and without justification.",
          howto_title: "How-To",
          howto_video_url: "https://polybox.ethz.ch/index.php/s/NIc3mPbapC9Rj5q",
          howto_video_title: "How-To Video",
          howto_summary: "Welcome onboard! Imagine you are flying over the most beautiful rivers! Support us to improve the algorithm in detecting the most pristine river sections from satellite image and making conservation plans!",
          howto_para01: "Please examine the image and try to answer the questions of what do you find in the image.",
          howto_para02: "By Pristine river, we mean the rivers are still remining undisturbed and most natural state.",
          howto_para03: "Click the info icon on the top-right corner to get some hints, if you are not sure about the answers.",
          howto_para04: " ",
          howto_para05: "",
          howto_para05_bold: "",
          howto_link_description: "Here you can read our",
          howto_link_url: "",
          howto_link_text: "Prinstine river Definition",
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
