<template>
  <Header :header="this.header" />
  <div class="content-container">
    <section class="section-container" id="missions" style="width:435px; height:714px;">
      <div class="section-header clipped-medium-backward">
        <img src="/icons/mission-icon.svg" />
        <h1>Mission Log</h1>
      </div>
      <div class="section-content-container">
        <h3>Current Assignment</h3>
        <Markdown :source="current_md" class="markdown" />
        <h3>Mission List</h3>
        <div class="mission-list-container">
          <Mission
            v-for="item in this.missions"
            :key="item.slug"
            :mission="item"
            :selected="this.mission_slug"
            @click="selectMission(item)"
          />
        </div>
      </div>
    </section>
    <section class="section-container" id="events" style="width:435px; height:714px;">
      <div class="section-header clipped-medium-backward">
        <img src="/icons/events-icon.svg" />
        <h1>Events Log</h1>
      </div>
      <div class="section-content-container">
        <Markdown :source="events" class="markdown" />
      </div>
    </section>
    <section class="section-container" id="pilots" style="width:894px; height:714px;">
      <div style="height:52px; overflow:hidden;">
        <div class="section-header clipped-medium-backward-pilot">
          <img src="/icons/pilot-icon.svg" />
          <h1>Pilot Roster</h1>
        </div>
        <div class="rhombus-back">&nbsp;</div>
      </div>
      <div class="section-content-container">
        <div class="pilot-list-container">
          <Pilot v-for="item in this.pilots" :key="item.slug" :pilot="item" />
        </div>
      </div>
    </section>
  </div>
  <svg
    style="visibility: hidden; position: absolute;"
    width="0"
    height="0"
    xmlns="http://www.w3.org/2000/svg"
    version="1.1"
  >
    <defs>
      <filter id="round">
        <feGaussianBlur in="SourceGraphic" stdDeviation="5" result="blur" />
        <feColorMatrix
          in="blur"
          mode="matrix"
          values="1 0 0 0 0  0 1 0 0 0  0 0 1 0 0  0 0 0 19 -5"
          result="goo"
        />
        <feComposite in="SourceGraphic" in2="goo" operator="atop" />
      </filter>
    </defs>
  </svg>
  <audio autoplay>
    <source src="/startup.ogg" type="audio/ogg" />
  </audio>
  <Footer/>
</template>

<script>
import Header from './components/layout/Header.vue';
import Footer from './components/layout/Footer.vue';
import Mission from './components/Mission.vue';
import Pilot from './components/Pilot.vue';
import Markdown from 'vue3-markdown-it';

export default {
  components: {
    Header,
    Footer,
    Mission,
    Pilot,
    Markdown
  },

  data() {
    return {
      "mission_slug": "002",
      "current_md": "",
      "events": "",
      "missions": [
        {
          "slug": "002",
          "name": "The Drop",
          "status": "start"
        },
      ],
      "pilots": [
        {
          "callsign": "Flea",
          "alias": "Rodger Dodger",
          "code": "f2ec7df5-a04b-4109-b920-99ed71d5c338//NDL-C-LUNAR-STELE",
          "corpro": "GMS",
          "frame": "Everest",
          "mech": "The Dodgy Rodger"
        },
        {
          "callsign": "Sleeper",
          "alias": "Iron",
          "code": "67857121-f7bc-4184-844a-8a72da907015//NDL-C-STONE-NOVEMBER",
          "corpro": "GMS",
          "frame": "Chomolungma",
          "mech": "DingDong"
        },
        {
          "callsign": "Sweetcheeks",
          "alias": "Eunice  VanDersnoot",
          "code": "82fe4cca-76cc-47a4-89b4-7183904eaf7d//NDL-C-BETA-SUN",
          "corpro": "GMS",
          "frame": "Everest",
          "mech": "Bulldozer Slate"
        },
        {
          "callsign": "Deadfire",
          "alias": "Alicia Verdance",
          "code": "c765f93d-e473-4a5f-838f-09b3b2599cd4//NDL-C-EARNEST-VERDICT",
          "corpro": "GMS",
          "frame": "Chomolungma",
          "mech": "LeLain"
        },
      ],
      "header": {
        "planet": "Cressidium",
        "year": "5016u",
        "system": "Colditas-2",
        "gate": "Cascade-Hood",
        "ring": "Cascade-Line",
        "headerTitle": "UNS-CV",
        "headerSubtitle": "RIO GRAND",
        "subheaderTitle": "Mechanized Cavalry Unit",
        "subheaderSubtitle": "Arrow Squad",
      },
      "options":{
        "eventsMarkdownPerMission": true
      }
    }
  },

  created() {
    this.loadMissionMarkdown()
    this.loadEventsMarkdown()
  },

  computed: {

  },

  methods: {
    selectMission(mission) {
      this.mission_slug = mission.slug;
      this.loadMissionMarkdown()
      if(this.options.eventsMarkdownPerMission){
        this.loadEventsMarkdown();
      }
    },
    loadMissionMarkdown() {
      let self = this;
      let md = `/missions/${self.mission_slug}.md`
      var client = new XMLHttpRequest();
      client.open('GET', md);
      client.onreadystatechange = function () {
        self.current_md = client.responseText;
      }
      client.send();
    },
    loadEventsMarkdown() {
      let self = this;
      let md = "";

      if(self.options.eventsMarkdownPerMission){
        md = `/events/${self.mission_slug}.md`
      }
      else {
        md = "/events.md"
      }

      var client = new XMLHttpRequest();
      client.open('GET', md);
      client.onreadystatechange = function () {
        self.events = client.responseText;
      }
      client.send();
    }
  }

}
</script>


<style lang="scss">
#app {
  width: 1902px;
  height: 910px;
  overflow: hidden;
}
</style>
