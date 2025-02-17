  { 
      template: `
            <!-- This template use https://bootstrap-vue.js.org/ -->
            <div v-if="pybossa.userProgressInPercent < 100"">
            <b-row>
            <!-- right columns - Media -->
            <b-col xl="6" lg="12" md="12" sm="12">
            <div @click="submitAnswer('A')">
              <maps
                class="my-2"
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
          
          <!-- Skip button -->
          <b-button @click="skip" variant="light" class="mt-3"
            >{{$t('skip-btn')}}
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
          questions:[],
          markedPlaces:[],
          area:{"latlngs":[]},
          answers: [],
          zoom: 15,
          showAlert: false,
          questionList: [],
        }},
      
        methods: {
          submitAnswer: function(answer){
              this.answers = answer;
              this.submit();
          },
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
