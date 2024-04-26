<template>
  <Header :header="this.header" />
  <div class="content-container">
    <section class="section-container" id="missions" style="width:435px; height:714px;">
      <div class="section-header clipped-medium-backward">
        <img src="/icons/mission-icon.png" />
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
        <img src="/icons/events-icon.png" />
        <h1>Events Log</h1>
      </div>
      <div class="section-content-container">
        <Markdown :source="events" class="markdown" />
      </div>
    </section>
    <section class="section-container" id="pilots" style="width:894px; height:714px;">
      <div style="height:52px; overflow:hidden;">
        <div class="section-header clipped-medium-backward-pilot">
          <img src="/icons/pilot-icon.png" />
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
      "mission_slug": "008",
      "current_md": "008",
      "events": "008",
      "missions": [
        {
          "slug": "001",
          "name": "The Drop",
          "status": "success"
        },
        {
          "slug": "002",
          "name": "Daybreak",
          "status": "success"
        },
        {
          "slug": "003",
          "name": "Cybersleuth",
          "status": "success"
        },
        {
          "slug": "004",
          "name": "Dustgrave",
          "status": "success"
        },
        {
          "slug": "005",
          "name": "Phantom Pursuit",
          "status": "partial-success"
        },
        {
          "slug": "006",
          "name": "Genetic Veil",
          "status": "success"
        },
        {
          "slug": "007",
          "name": "Defiant Dawn",
          "status": "success"
        },
        {
          "slug": "008",
          "name": "TBD",
          "status": "start"
        },
      ],
      "pilots": [
        {
          "callsign": "scimitar",
          "alias": "Zara Khan",
          "code": "Khan.Zara:5f5a521c-4b4d-654d0-2q34dfs-03245//NDL-C-GAMMA-SUN",
          "corpro": "HA",
          "frame": "Arthur",
          "mech": "Gentlement, Please, Stop"
        },
        {
          "callsign": "junior",
          "alias": "J'lio Kimoto III",
          "code": "Kimoto.J'lio:6dab8071-6d42-486d-b433-0b77c295f6f4//NDL-C-NULL-ROYAL",
          "corpro": "DFG",
          "frame": "Gluttony",
          "mech": "Drowned in Love"
        },
        {
          "callsign": "shrike",
          "alias": "Dalton Burke",
          "code": "Burke.Dalton:a96d415d-6321-4a61-8509-edb7f6ed4280//NDL-C-SORROW-HAMMER",
          "corpro": "SSC",
          "frame": "Oleander",
          "mech": "Charon"
        },
        {
          "callsign": "dominus",
          "alias": "Rho",
          "code": "Rho:d826efe6-e91fe91f-4617-9ad0-02d6-df1e92ebe//NDL-C-COLD-AXE",
          "corpro": "HA",
          "frame": "Tokugawa",
          "mech": "Abaddon"
        },
      ],
      "header": {
        "planet": "Rainier Station",
        "year": "5016u",
        "system": "n/a",
        "gate": "Rainier Station",
        "ring": "Cascade Line",
        "headerTitle": "Union Naval Department",
        "headerSubtitle": "",
        "subheaderTitle": "UNS-CV Rio Grande ",
        "subheaderSubtitle": "Charlie Foxtrot",
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
