{ 
    template: `
          <!-- This template use https://bootstrap-vue.js.org/ -->
    
          <b-row v-if="pybossa.userProgressInPercent < 100">
          <!-- Form zone -->
          <b-col md="4" class="mt-4 mt-md-0 order-1 order-md-1">
            <!-- Questions with answers -->
            <b-form-group
              :key="question.id"
              v-for="question in questionList"
              label-size="lg"
              class="mt-2 mb-4"
            >
              <label>
                {{question.question}}
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
            <b-button size="lg" @click="submit" variant="success" class="mt-3"
              >{{$t('submit-btn')}}
            </b-button>
            
            <!-- Skip button -->
            <b-button @click="skip" variant="light" class="mt-3"
              >{{$t('skip-btn')}}
            </b-button>

            <!-- Submit button
            <b-button size="lg" @click="submit" variant="secondary" class="mt-3"
              > Right
            </b-button>
            -->
    
            
    
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
    
          <!-- right columns - Media -->
          <b-col md="4" class="mt-4 mt-md-0 order-0 order-md-0">
            <maps
              class="my-2"
              :hideInteraction="false"
              :resetToInitial="false"
              :hideIcons="true"
              :scrollToZoom="false"
              style="height: 40vh; width: 40vh;"
              :mapSettings="mapSettingsA"
              :taskLoaded="pybossa.taskLoaded"
            ></maps>
          </b-col>
          <b-col md="4" class="mt-4 mt-md-0 order-2 order-md-2">
            <maps
              class="my-2"
              :hideInteraction="false"
              :resetToInitial="false"
              :hideIcons="true"
              :scrollToZoom="false"
              style="height: 40vh; width: 40vh; "
              :mapSettings="mapSettingsB"
              :taskLoaded="pybossa.taskLoaded"
            ></maps>
          </b-col>
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
        questions:[{"id":1,"question":"Choose the image corresponding to the river with the most natural aspect","answers":["Left","Right"],"type":"multiple_choice","required":true,"isDependent":false,"condition":{}}],
        markedPlaces:[],
        area:{"latlngs":[]},
        answers: [],
        zoom: 17,
        showAlert: false,
        questionList: [],
      }},
    
      methods: {
        submit: function(){this.isFormValid()?(this.pybossa.saveTask(this.answers),this.initialize()):this.showAlert=!0},
        skip: function(){this.pybossa.skip(),this.initialize()},
        isFormValid: function(){var t=this,e=!0;return this.questionList.every(function(s){var i=t.answers.find(function(t){return t.qid==s.id})||[];return!(s.required&&(!i.value||i.value.length<=0))||(e=!1,!1)}),e},
        initialize: function(){this.showAlert=!1;var t=this.pybossa;this.questionList=this.questions.filter(function(e){return t.isConditionEmpty(e)}),this.answers=this.questions.map(function(t){var e={qid:t.id,question:t.question,value:null};return"multiple_choice"===t.type&&(e.value=[]),e}),window.scrollTo({top:0,behavior:"smooth"})},
        },
      
    
        computed: {
          
          task: function(){return this.pybossa.task},
          taskInfo: function(){return this.task.info},
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