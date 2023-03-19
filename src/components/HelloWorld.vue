<template>
  <b-container>
    <h2 class="mt-4">Average Wages</h2>
    <b-row>
      <b-col cols="4">
        <b-form-input
          id="inline-form-input-name"
          class="mb-2 mr-sm-2 mb-sm-0"
          placeholder="Team IDs"
          v-model="teamIds"
        ></b-form-input>
      </b-col>
      <b-col cols="4">
        <b-form-checkbox v-model="under" class="mb-2 mr-sm-2 mb-sm-0">U1000</b-form-checkbox>
      </b-col>
      <b-col cols="4">
        <b-button variant="primary" @click="getTeams">Get Details</b-button>
      </b-col>
    </b-row>

    <b-table striped hover :items="wageTable"></b-table>
    <h2 class="mt-4">Match Stats</h2>
    <b-row>
      <b-col cols="4">
        <b-form-input
          id="inline-form-input-name"
          class="mb-2 mr-sm-2 mb-sm-0"
          placeholder="Match IDs"
          v-model="matchIds"
        ></b-form-input>
      </b-col>
      <b-col cols="4">
        <b-button variant="primary" @click="getMatch">Get Details</b-button>
      </b-col>
    </b-row>
    <h3 class="mt-4">Batting</h3>
    <b-table striped hover :items="matchTable.batting"></b-table>
    <h3 class="mt-4">Bowling</h3>
    <b-table striped hover :items="matchTable.bowing"></b-table>
    <h3 class="mt-4">Team</h3>
    <b-table striped hover :items="matchTable.team"></b-table>
  </b-container>
</template>

<script>
import axios from "axios";

export default {
  name: "HelloWorld",
  computed: {
    wageTable() {
      var wageTable = [];
      var headers = this.wages[0].split(",");
      for (var i = 1; i < this.wages.length; i++) {
        var data = this.wages[i].split(",");
        var obj = {};
        for (var j = 0; j < data.length; j++) {
          obj[headers[j].trim()] = data[j].trim();
        }
        wageTable.push(obj);
      }
      return wageTable;
    },
    matchTable() {
      var matchtable = {
        batting: [],
        bowing: [],
        team: [],
      };

      var headers = this.stats["batting"][0].split(",");
      for (var i = 1; i < this.stats["batting"].length; i++) {
        var data = this.stats["batting"][i].split(",");
        var obj = {};
        for (var j = 0; j < data.length; j++) {
          obj[headers[j].trim()] = data[j].trim();
        }
        matchtable["batting"].push(obj);
      }

      var headers2 = this.stats["bowing"][0].split(",");
      for (var i2 = 1; i2 < this.stats["bowing"].length; i2++) {
        var data2 = this.stats["bowing"][i2].split(",");
        var obj2 = {};
        for (var j2 = 0; j2 < data2.length; j2++) {
          obj2[headers2[j2].trim()] = data2[j2].trim();
        }
        matchtable["bowing"].push(obj2);
      }

      var headers3 = this.stats["team"][0].split(",");
      for (var i3 = 1; i3 < this.stats["team"].length; i3++) {
        var data3 = this.stats["team"][i3].split(",");
        var obj3 = {};
        for (var j3 = 0; j3 < data3.length; j3++) {
          obj3[headers3[j3].trim()] = data3[j3].trim();
        }
        matchtable["team"].push(obj3);
      }

      return matchtable;
    },
  },
  data() {
    return {
      teamIds: "0",
      under: false,
      matchIds: "0",
      wages: [
        "TeamID, TeamName, Average, Median, NumberOfPlayers",
        "Dummy,Dummy,Dummy,Dummy,Dummy",
      ],
      stats: {
        batting: [
          "PlayerID, Name, Team(s), Matches, Innings, Runs, Balls, NO, Average, StrikeRate, 4s, 6s, 50s, 100s, HS",
          "Dummy,Dummy,Dummy,Dummy,Dummy,Dummy,Dummy,Dummy,Dummy,Dummy,Dummy,Dummy,Dummy,Dummy,Dummy",
        ],
        bowing: [
          "PlayerID, Name, Team(s), Matches, Overs, Balls, Maidens, Runs, Wickets, Average, StrikeRate, Economy, NoBalls, Wides, 5wi, Best",
          "Dummy,Dummy,Dummy,Dummy,Dummy,Dummy,Dummy,Dummy,Dummy,Dummy,Dummy,Dummy,Dummy,Dummy,Dummy,Dummy",
        ],
        team: [
          "TeamID, Team, Total, Wins, Losses, Ties, WinPercentage",
          "Dummy,Dummy,Dummy,Dummy,Dummy,Dummy,Dummy",
        ],
      },
      host: "https://battrickstats-d4otn6fbfa-uc.a.run.app/",
    };
  },
  methods: {
    async getTeams() {
      console.log(this.teamIds);
      const headers = {
        headers: {
          "Content-Type": "application/json",
        },
      };
      const requestBody = {
        U1000: this.under,
        teamIDs: this.teamIds.split(",").map(Number),
      };
      console.log(requestBody);
      await axios
        .post(this.host + "averageWages", requestBody, headers)
        .then((response) => {
          console.log(response.data.wages);
          this.wages = response.data.wages;
        })
        .catch(console.log("Error"));
    },
    async getMatch() {
      const headers = {
        headers: {
          "Content-Type": "application/json",
        },
      };
      const requestBody = {
        matchIDs: this.matchIds.split(",").map(Number),
      };
      await axios
        .post(this.host + "stats", requestBody, headers)
        .then((response) => {
          console.log(response.data);
          this.stats = response.data;
        })
        .catch(() => {
          this.$bvToast.toast(`Error while retrieving stats`, {
            title: "Retrieve Failed",
            autoHideDelay: 500000,
            appendToast: true,
            noCloseButton: true,
            variant: "danger",
          });
        });
    },
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
</style>
