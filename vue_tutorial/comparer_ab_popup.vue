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
          <b-col xl="6" lg="12" md="12" sm="12" class="d-flex justify-content-center align-items-center">
          
          <div 
          :style="{height: imgSize + 'px' , width: imgSize + 'px'}" 
          :style="[ hoverA ? { border: 'black solid 5px'} : {border: 'white solid 5px'} ]">
            <maps
              class="my-2"
              :resetToInitial="false"
              :hide-icons="true"
              :hide-labels="true"
              :scroll-to-zoom="false"
              :hide-interaction="true"
              :hide-geo-search="true"
              :style="{ height: imgSize + 'px' , width: imgSize + 'px'}"
              :mapSettings="mapSettingsA"
              :taskLoaded="pybossa.taskLoaded"
            ></maps>
            </div>
            <!-- Hover Effect A -->
            <div 
            @click="submitAnswer('A')" 
            @mouseover="hoverA = true"
            @mouseleave="hoverA = false"
            :style="{height: imgSize + 'px' , width: imgSize + 'px'}" 
            style="position: absolute;">
                <p v-if="hoverA == true" style="font-size: 144px; color: white; opacity: 0.5;" 
                class="text-center w-100 h-100">{{ text.textImageA }}</p>
            </div>
  
  
          </b-col>
          <b-col  xl="6" lg="12" md="12" sm="12" class="d-flex justify-content-center align-items-center">
            <div 
              :style="{height: imgSize + 'px' , width: imgSize + 'px'}" 
              :style="[ hoverB ? { border: 'black solid 5px'} : {border: 'white solid 5px'} ]">
            <maps
              class="my-2"
              :resetToInitial="false"
              :hide-icons="true"
              :hide-labels="true"
              :scroll-to-zoom="false"
              :hide-interaction="true"
              :hide-geo-search="true"
              :style="{ height: imgSize + 'px' , width: imgSize + 'px'}"
              :mapSettings="mapSettingsB"
              :taskLoaded="pybossa.taskLoaded"
            ></maps>
            </div>
  
  
            <!-- Hover Effect B -->
            <div 
            @click="submitAnswer('B')" 
            @mouseover="hoverB = true"
            @mouseleave="hoverB = false"
            :style="{height: imgSize + 'px' , width: imgSize + 'px'}" 
            style="position: absolute;">
                <p v-if="hoverB == true" style="font-size: 144px; color: white; opacity: 0.5;" 
                class="text-center w-100 h-100">{{ text.textImageB }}</p>
            </div>
   
          </b-col>
  
        </b-row>
  
  
  
  
        </b-row>
  
        <!-- Form zone -->
        <b-row align-h="center">
        
          <b-col xl="6" lg="12" md="12" sm="12" class="d-flex justify-content-center text-center" >
            <!-- Left button -->
            <b-button size="lg" :style="{width: imgSize + 'px'}" style="font-size: 24px" @click="submitAnswer('A')" variant="success" class="m-3"
              >{{ text.textImageA }}
            </b-button>
          </b-col>
            
          <b-col xl="6" lg="12" md="12" sm="12" class="d-flex justify-content-center text-center" >
            <!-- Right button -->
            <b-button size="lg" :style="{width: imgSize + 'px'}" style="font-size: 24px" @click="submitAnswer('B')" variant="success" class="m-3"
              > {{ text.textImageB }}
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
            <b-badge variant="primary">{{ userProgressAdapted }}</b-badge>
            {{$t('template-editor-text-2a')}}
            <b-badge variant="primary">{{ taskSlice }}</b-badge>
            {{$t('template-editor-text-3')}}
          </p>
  
            <b-progress :value="userProgressAdapted / taskSlice * 30" :max="30"></b-progress>
  
          </b-col>
  
  
        <b-modal 
          v-model="modalProgressShow" @hidden="if(!modalQuit){modalFlag = false}">
          <b-card no-body class="border-0 text-center">
            <b-card-text>
              <p>{{ text.tl_header }}</p>
              <p>{{ text.tl_text_a }} <strong>{{ pybossa.userProgress.done }}</strong> {{ text.tl_text_b }}</p>
              <p>{{ text.tl_question }}</p>
            </b-card-text>
          </b-card>
          <template #modal-footer>
              <b-button variant="light" @click="quitPresenter()">
                {{ text.tl_no }}
              </b-button>
              <b-button variant="success" @click="modalFlag = false">
                {{ text.tl_yes }}
              </b-button>
        </template>
        </b-modal>
  
  
  
  
  
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
                    <h5>{{ text.howto_en }}</h5>
                    <p>{{ text.howto_summary }}</p>
                    <p>{{ text.howto_summary02 }}</p>
                    <h5>{{ text.howto_fr }}</h5>
                    <p>{{ text.howto_summary_fr }}</p>
                    <p>{{ text.howto_summary_fr02 }}</p>
                    <h5>{{ text.howto_de }}</h5>
                    <p>{{ text.howto_summary_de }}</p>
                    <p>{{ text.howto_summary_de02 }}</p>
                    <h5>{{ text.howto_es }}</h5>
                    <p>{{ text.howto_summary_es }}</p>
                    <p>{{ text.howto_summary_es02 }}</p>
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
                    <p>{{ text.howto_para_fr }}</p>
                    <p>{{ text.howto_para_de }}</p>
                    <p>{{ text.howto_para_es }}</p>
                    <p>
                      <a :href="text.howto_link_url" target=”_blank”>{{ text.howto_link_text }}</a>
                      {{ text.howto_link_description }}
                    </p>
                    <p>
                      <a :href="text.howto_link_url" target=”_blank”>{{ text.howto_link_text_fr}}</a>
                      {{ text.howto_link_description_fr }}
                    </p>
                    <p>
                      <a :href="text.howto_link_url" target=”_blank”>{{ text.howto_link_text_de }}</a>
                      {{ text.howto_link_description_de }}
                    </p>
                    <p>
                      <a :href="text.howto_link_url" target=”_blank”>{{ text.howto_link_text_es }}</a>
                      {{ text.howto_link_description_es }}
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
                        <p>{{ text.soc_withdraw_p_de }}</p>
                  </b-card-text>
                  <b-card-text>
                    <h4>{{ text.soc_consent_title_de }}</h4>
                        {{ text.soc_consent_list_header_de }}
                        <ul style="list-style-type: disc; margin-left: 30px">
                          <li>{{ text.soc_consent_list_1_de }}</li>
                          <li>{{ text.soc_consent_list_2_de }}</li>
                          <li>{{ text.soc_consent_list_3_de }}</li>
                        </ul>
                  </b-card-text>
                  <b-card-text>
                        <h4>{{ text.soc_privacy_title }}</h4>
                        <p>{{ text.soc_privacy_p1_de }}</p>
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
        modalQuit: false,
        modalFlag: false,
        taskSlice: 30,
        tabIndex: 0,
        questions:[],
        area:{"latlngs":[]},
        answers: [],
        zoom: 15,
        imgSize: 700,
        hoverA: false,
        hoverB: false,
        color: 'black',
        showAlert: false,
        questionList: [],
        translations: {
          "de": {
            tl_header: "Vielen Dank für Ihre Teilnahme!",
            tl_text_a: "Sie haben",
            tl_text_b: "Aufgaben erledigt.",
            tl_question: "Möchten Sie mehr Aufgaben?",
            tl_no: "Nein, Danke.",
            tl_yes: "Ja!",
            task: " Bitte klicken Sie auf den wildesten Fluss. ",
            soc_skip: "Start",
            textImageA: "A",
            textImageB: "B",
            soc_continue: "Weiter",
            soc_back: "Zurück",
            soc_greeting: "Wild River Spiel            ",
            soc_withdraw_p_de: "Ihre Teilnahme an der Studie ist freiwillig.",
            soc_consent_title_de: "Einverständniserklärung",
            soc_consent_list_header_de: "Mit der Teilnahme an der Studie bestätige ich, dass ich:",
            soc_consent_list_1_de: "die Studieninformationen gehört/gelesen und verstanden habe.",
            soc_consent_list_2_de: "genügend Zeit hatte, mich für meine Teilnahme an der Studie zu entscheiden.",
            soc_consent_list_3_de: "freiwillig an der Studie teilnehme und damit einverstanden bin, dass meine persönlichen Daten wie unten beschrieben verwendet werden.",
            soc_privacy_p1_de: "Die Daten der Teilnehmer werden mit äußerster Vertraulichkeit behandelt. Alle gesammelten Daten, einschließlich, aber nicht beschränkt auf demografische Angaben, Antworten auf Umfragefragen, und andere relevante Informationen, werden sicher gespeichert und sind nur autorisiertem Personal zugänglich, das an der Forschung beteiligt ist. 
            Ihre persönliche Identität wird streng vertraulich behandelt, und alle veröffentlichten Ergebnisse werden in zusammengefasster Form dargestellt, um sicherzustellen, dass einzelne Teilnehmer nicht identifiziert werden können. 
            Außerdem werden Ihre Daten nicht an Dritte weitergegeben und nur für die spezifischen Forschungszwecke verwendet, die auf der Einführungsseite vor der Teilnahme an der Studie beschrieben sind.",
            soc_withdraw_p_de: "Ihre Teilnahme an der Studie ist freiwillig.",
            howto_title: "Wie zu tun",
            howto_video_url: "https://www.youtube.com/embed/5r2_TihkXUY?si=yzz2-cQAEjlu4BGS",
            howto_video_title: "How-To Video",
            howto_summary_de: "Hallo Flussliebhaber, willkommen an Bord! Wir werden jetzt gemeinsam über wunderschöne Flüsse fliegen und beeindruckende, wilde Flusslandschaften fotografieren. Macht euch bereit, wir heben ab!",
            howto_summary_de02: "Wildflüsse? Wildflüsse sind Flussabschnitte, die nicht gestört werden und daher einen unglaublich hohen ökologischen Wert haben. Um zukünftig bessere Schutzpläne erstellen zu können, brauchen wir nun Ihre wertvolle Unterstützung, um einen Algorithmus zur Erkennung von wilden Flussabschnitten anhand von Satellitenbildern zu verbessern.", 
            howto_para_de: "Bitte vergleichen Sie die beiden Bilder des Flusses und wählen Sie das wildeste (natürlichste) Bild, indem Sie direkt auf das Bild klicken.", 
            howto_link_description_de: " können Sie mehr über unser Projekt lesen.",
            howto_link_url: "https://ele.ethz.ch/research/technology-modelling/citizen-river.html",
            howto_link_text_de: "Hier",
            submit_btn: "Weiter",
            skip_btn: "Überspringen",
            howto_btn: "How-To",
            disclaimer_btn: "Disclaimer",
          },
        }
      }},
    
      methods: {
        quitPresenter: function(){
            this.modalQuit = true;
            this.$router.push({ name: 'project', params: { id: this.pybossa.id} });
        },
        

        submitAnswer: function(answer){
            this.answers = answer;
            this.submit();
            if (this.userProgressLimited != 0){this.modalFlag = true; this.modalQuit = false;};
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
          userProgressLimited: function(){return this.pybossa.userProgress.done % this.taskSlice;},
          userProgressAdapted: function(){
            if (this.userProgressLimited == 0 && this.modalFlag){
              return this.taskSlice
            } else {
              return this.userProgressLimited;
            }
          },
          modalProgressShow: function(){return !this.modalQuit && this.modalFlag && this.userProgressLimited == 0},
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
      }, 
    }