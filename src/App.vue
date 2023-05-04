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
      "mission_slug": "001",
      "current_md": "001",
      "events": "001",
      "missions": [
        {
          "slug": "001",
          "name": "The Drop",
          "status": "start"
        },
      ],
      "pilots": [
        {
          "callsign": "bulwark",
          "alias": "Mischa Kovalsky",
          "code": "Kovalsky.Mischa:a781f8cf-4891-49aa-b32e-adcbdc876637//NDL-C-FIRST-CUT",
          "corpro": "GMS",
          "frame": "Sagarmatha",
          "mech": "Collective Bargain"
        },
        {
          "callsign": "druzhina",
          "alias": "Illya Karpenko",
          "code": "Karpenko.Illya:fc0f8db8-ecd4-4c32-13556b79d853//NDL-C-SECOND-WILL",
          "corpro": "GMS",
          "frame": "Sagarmatha",
          "mech": "Oathkeeper"
        },
        {
          "callsign": "kid fritz",
          "alias": "Frederick 'Fritz' Ackerley",
          "code": "Ackerley.Frederick.Fritz:652597be-f3fd-45f3-82c3-f851b2cbb7e4//NDL-C-THIRD-JUNE",
          "corpro": "GMS",
          "frame": "Chomolungma",
          "mech": "Phantom of the Omninet"
        },
        {
          "callsign": "shrike",
          "alias": "Dalton Burke",
          "code": "Burke.Dalton:a96d415d-6321-4a61-8509-edb7f6ed4280//NDL-C-SORROW-HAMMER",
          "corpro": "GMS",
          "frame": "Everest",
          "mech": "Pale Horse"
        },
        {
          "callsign": "columbia",
          "alias": "Nay Cough",
          "code": "Cough.Nay:aefb052e-0a91-49e0-b961-38f545c18ba5//NDL-C-OMEGA-GRAVE",
          "corpro": "GMS",
          "frame": "Sagarmatha",
          "mech": "Bloodied Honor"
        },
      ],
      "header": {
        "planet": "Nov Elysia, Cressidium",
        "year": "5016u",
        "system": "Cascade-16",
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
