<template>
  <div>
    <h1>Create League</h1>
    <input v-model.number="numberOfTeams" type="number" min="2" max="255" placeholder="Enter number of teams" />
    <button @click="createLeague">Create League</button>

    <div v-if="leagueData">
      <h2>Teams</h2>
      <table>
        <thead>
        <tr>
          <th>Team Name</th>
          <th>Games Played</th>
          <th>Wins</th>
          <th>Draws</th>
          <th>Losses</th>
          <th>Goals Scored</th>
          <th>Goals Conceded</th>
          <th>Points</th>
        </tr>
        </thead>
        <tbody>
        <tr v-for="team in leagueData.standings || []" :key="team.team.id">
          <td>{{ team.team.name }}</td>
          <td>{{ team.games_played || 0 }}</td>
          <td>{{ team.wins || 0 }}</td>
          <td>{{ team.draws || 0 }}</td>
          <td>{{ team.losses || 0 }}</td>
          <td>{{ team.goals_scored || 0 }}</td>
          <td>{{ team.goals_conceded || 0 }}</td>
          <td>{{ team.points || 0 }}</td>
        </tr>
        </tbody>
      </table>
      <button v-if="leagueData.next_round > 0" @click="playRound">Play Next Round (Week {{ leagueData.next_round }})</button>

      <div v-if="leagueData.games && leagueData.games.length">
        <h2>Games This Week</h2>
        <table>
          <thead>
          <tr>
            <th>Team A</th>
            <th>Team B</th>
            <th>Score</th>
            <th>Week</th>
          </tr>
          </thead>
          <tbody>
          <tr v-for="game in leagueData.games" :key="game.id">
            <td>{{ game.team_a.name }}</td>
            <td>{{ game.team_b.name }}</td>
            <td>{{ game.score_team_a }} - {{ game.score_team_b }}</td>
            <td>{{ game.week }}</td>
          </tr>
          </tbody>
        </table>
      </div>
    </div>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  data() {
    return {
      numberOfTeams: 2,
      leagueData: null,
    };
  },
  methods: {
    async createLeague() {
      try {
        const response = await axios.post('http://localhost/api/create-league', {
          number_of_teams: this.numberOfTeams,
        });
        this.leagueData = response.data;
      } catch (error) {
        console.error('Error creating league:', error);
      }
    },
    async playRound() {
      try {
        const response = await axios.post(`http://localhost/api/league/${this.leagueData.league_id}/play-round/${this.leagueData.next_round}`);
        this.leagueData.standings = response.data.standings;
        this.leagueData.next_round = response.data.next_round;
        this.leagueData.games = response.data.games;
      } catch (error) {
        console.error('Error playing round:', error);
      }
    },
  },
};
</script>

<style scoped>
table {
  width: 100%;
  border-collapse: collapse;
}

th, td {
  border: 1px solid #ddd;
  padding: 8px;
}

th {
  background-color: #f2f2f2;
  text-align: left;
}

button {
  margin-top: 10px;
}
</style>