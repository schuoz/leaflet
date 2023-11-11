{ 
    template: `
          <!-- This template use https://bootstrap-vue.js.org/ -->
          <div v-if="pybossa.userProgressInPercent < 100"">
    
  
          <b-row class="w-100" align-h="start">
  
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
  
          <!-- Question -->
          <b-row align-h="center" class="mt-3 mb-3">
              <b-card bg-variant="light" class="border-0">
                  <b-card-text :id="taskInfo.ID" >
                        <h4>{{ text.task }}</h4>
                  </b-card-text>
              </b-card>
          </b-row>
  
          <b-row>
          <!-- right columns - Media -->
          <b-col xl="6" lg="12" md="12" sm="12">
          <div @click="submitAnswer('A')" >
            <maps
              class="my-2"
              :resetToInitial="false"
              :hide-icons="true"
              :hide-labels="true"
              :scroll-to-zoom="false"
              :hide-interaction="true"
              :hide-geo-search="true"
              style="height: 200px; width: 200px; "
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
              :hide-labels="true"
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
  
  
  
  
        </b-row>
  
        <!-- Form zone -->
        <b-row align-h="center">
        
          <b-col xl="6" lg="8" md="8" sm="12" class="d-flex justify-content-center text-center" >
  
            <!-- Left button -->
            <b-button block size="lg" @click="submitAnswer('A')" variant="success" class="m-3"
              >First
            </b-button>
            
            <!-- Right button -->
            <b-button block size="lg" @click="submitAnswer('B')" variant="success" class="m-3"
              > Last
            </b-button>
          </b-col>
  
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
                    <p>
                      {{ text.howto_link_description }}
                      <a :href="text.howto_link_url" target=”_blank”>{{ text.howto_link_text }}</a>
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
            task: " Please click on the most wild river to you. (There is no correct answer)",
            soc_skip: "Start",
            soc_continue: "Continue",
            soc_back: "Back",
            soc_greeting: "Wild river match",
            soc_withdraw_p1: "Your participation in the study is voluntary.",
            soc_consent_title: "Statement of consent",
            soc_consent_list_header: "By participating in the study, I confirm that I:",
            soc_consent_list_1: "have heard/read and understood the study information.",
            soc_consent_list_2: "had enough time to decide on my participation in the study.",
            soc_consent_list_3: "voluntarily participate in the study and agree to my personal data being used as described below.",
            howto_title: "How-To",
            howto_video_url: "https://polybox.ethz.ch/index.php/s/NIc3mPbapC9Rj5q",
            howto_video_title: "How-To Video",
            howto_summary: "Welcome onboard! Imagine you are flying over the most beautiful rivers! Support us to improve the algorithm in detecting the most wild river sections from satellite image and making conservation plans!",
            howto_para01: "Please compare the two images of river, and choose the more wild (natural) one by <<clicking on>> the image directly.",
            howto_para02: "By Wild river, we mean the rivers are still remining undisturbed and most natural state.",
            howto_link_description: "Here you can read our",
            howto_link_url: "",
            howto_link_text: "Wild river Definition",
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