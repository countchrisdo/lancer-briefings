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
      "mission_slug": "001",
      "current_md": "001",
      "events": "001",
      "missions": [
        {
          "slug": "000",
          "name": "Operation Snakeskin",
          "status": "success"
        },
        {
          "slug": "001",
          "name": "Operation Text",
          "status": "start"
        },
      ],
      "pilots": [
        {
          "callsign": "Wildfire",
          "alias": "Serena Sherwood",
          "code": "Sf572259-a946-m1bf-031a-00543709e892a",
          "corpro": "Classified",
          "frame": "Classified",
          "mech": "Ghost_Town"
        },
        {
          "callsign": "Fever",
          "alias": "Zorica Novak",
          "code": "P1fdf62e-481e-ne10-cc7c8-ak3bc486011b",
          "corpro": "Classified",
          "frame": "Classified",
          "mech": "Mosquito"
        },
        {
          "callsign": "Jericho",
          "alias": 'Minerva "Minnie" Talos',
          "code": "D1fdf62e-381e-1e10-1c7c8-ak3bc486011c",
          "corpro": "Classified",
          "frame": "Classified",
          "mech": "Swan_Song"
        },
        {
          "callsign": "Kansas",
          "alias": 'Kaa Abgal',
          "code": "Jf572259-o946-a1bf-Q31a-u1m43709e892d",
          "corpro": "Data not Found",
          "frame": "Data not Found",
          "mech": "No_Love_Omninet"
        },
        {
          "callsign": "Singularity",
          "alias": 'Halcylia',
          "code": "E1fdf62e-n81e-ae10-07c8-0f3bc486011e",
          "corpro": "Classified",
          "frame": "Classified",
          "mech": "Realspace_Anomaly"
        },
      ],
      "header": {
        "planet": "Hercynia",
        "year": "5014u",
        "system": "Ardennes-3",
        "gate": "Atlas-Quanokrim",
        "ring": "Atlas-Line",
        "headerTitle": "Steel Hawks",
        "headerSubtitle": "Security LLC",
        "subheaderTitle": "Crisis Response",
        "subheaderSubtitle": "Coms-Host-Ram-Intel-Sigma",
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
