<template>
  <div id="app" class="hello">

    <h1>Welcome to the Serverless Voting App.</h1>
    <h4>Scenario 1.</h4>
    <h4>Select your response to scenario 1.</h4>

    <b-row align-h="center" class="mt-5">
      <b-card-group deck>
        <b-card bg-variant="success" text-variant="white" header="Option A" class="text-center" footer-tag="footer">
          <b-card-text>Option A.</b-card-text>
          <b-button size="lg" variant="primary"  @click="vote('yes')">Button</b-button>
          <em slot="footer">{{ votesYes }} voted</em>
        </b-card>

        <b-card bg-variant="danger" text-variant="white" header="Option B" class="text-center" footer-tag="footer">
          <b-card-text>Option B.</b-card-text>
          <b-button size="lg" variant="primary" @click="vote('no')">Button</b-button>
          <em slot="footer">{{ votesNo }} voted</em>
        </b-card>
      </b-card-group>
    </b-row>
    <b-row align-h="center" class="mt-5">
      <p>Questions? Ask Dan <a href="https://twitter.com/GermainDan">@GermainDan</a>.</p>
    </b-row>
  </div>  
</template>


<script>
import { API } from 'aws-amplify'
export default {
  name: 'app',
  data() {
    return {
      apiName: 'pollCounterAPI',
      votesYes: 0,
      votesNo: 0
    }
  },
  methods: {
    vote: async function (vote) {
      const init = {
        queryStringParameters: {
          vote
        }
      }
      const response = await API.post(this.apiName, '/votes', init)
      if (vote === 'yes') this.votesYes = response.data.Attributes.votesYes
      if (vote === 'no') this.votesNo = response.data.Attributes.votesNo
    },
    updateVotes: async function () {
      const response = await API.get(this.apiName, '/votes/poll-001')
      this.votesNo = response[0].votesNo
      this.votesYes = response[0].votesYes    
    }
  },
  created () {
    this.updateVotes()
    setInterval(this.updateVotes, 3000)
  }
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
