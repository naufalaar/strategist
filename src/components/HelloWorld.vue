<template>
  <b-container>
    <b-form class="mt-4">
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
          <b-button variant="primary" @click="getTeams">Get Details</b-button>
        </b-col>
      </b-row>
    </b-form>

    <b-table striped hover :items="wageTable"></b-table>
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
  },
  data() {
    return {
      teamIds: "0",
      wages: [
        "TeamID, TeamName, Average, Median, NumberOfPlayers",
        "Dummy,Dummy,Dummy,Dummy,Dummy",
      ],
      host: "https://battrickstats-d4otn6fbfa-uc.a.run.app/averageWages",
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
        U1000: true,
        teamIDs: this.teamIds.split(',').map(Number),
      };
      console.log(requestBody);
      await axios
        .post(this.host, requestBody, headers)
        .then((response) => {
          console.log(response.data.wages);
          this.wages = response.data.wages;
        })
        .catch(console.log("Error"));
    },
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
</style>
