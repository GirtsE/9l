<script>
    import { onMount, onDestroy } from 'svelte';
  
    let teamData = null;
    let playerData = null;
    let teamName = "The Smurfs"
    let teamRankings = [];
    let teammatesData = [];
    let playerRankings = [];
  
    const fetchData = async () => {
      try {
        const responseTeam = await fetch('https://nine-lives.vercel.app/api/team');
        const resultTeam = await responseTeam.json();
        const responsePlayer = await fetch('https://nine-lives.vercel.app/api/player');
        const resultPlayer = await responsePlayer.json();
        teamData = resultTeam;
        playerData = resultPlayer;
      } catch (error) {
        console.error('Error fetching data:', error);
      } finally{
        currentRanking();
      }
    };

    const currentRanking = async () => {
      try {
        const teamsArray = Object.entries(teamData).map(([teamName, data]) => ({ teamName, ...data }));
        const sortedTeams = teamsArray.sort((a, b) => b.score - a.score);
        sortedTeams.forEach((team, index) => {
            const { teamName, score, lives } = team;
            var ranking = "";
            if (index + 1 === 1) {
                ranking = `${index + 1}st`;
            } else if (index + 1 === 2) {
                ranking = `${index + 1}nd`;
            } else if (index + 1 === 3) {
                ranking = `${index + 1}rd`;
            } else {
                ranking = `${index + 1}th`;
            }
            teamRankings[teamName] = ranking;
        });

        const playerArray = Object.entries(playerData).map(([playerName, data]) => ({ playerName, ...data }));
        const sortedPlayers = playerArray.sort((a, b) => b.score - a.score);

        var teammatesNames = ["ZeeBeeBlue", "SonofAlister", "Clutchypoo"];
        sortedPlayers.forEach((playerName, index) => {
            var existingTeammateIndex = teammatesData.findIndex(teammate => teammate.name === playerName.playerName);
            const { player, score, deaths } = playerName;

            var ranking = "";
            if (index + 1 === 1) {
                ranking = `${index + 1}st`;
            } else if (index + 1 === 2) {
                ranking = `${index + 1}nd`;
            } else if (index + 1 === 3) {
                ranking = `${index + 1}rd`;
            } else {
                ranking = `${index + 1}th`;
            }
            playerRankings[playerName.playerName] = ranking;
            
            if (teammatesNames.includes(playerName.playerName)){
                var teammate = {
                    "name": playerName.playerName,
                    "score": playerName.score,
                    "deaths": playerName.deaths,   
                }
            
                if (existingTeammateIndex !== -1) {
                    teammatesData[existingTeammateIndex] = teammate;
                } else {
                    teammatesData.push(teammate);
                }
            }
        });
      } catch (error) {
        console.error('Error fetching data:', error);
      }
    };
  
    onMount(() => {
      fetchData();
      const interval = setInterval(fetchData, 60000);
      onDestroy(() => clearInterval(interval));
    });
  </script>
  
  <main>    
    {#if teamData}
      <div class="scoreboard-wrapper">
        <div class="title">{teamName} ({teamRankings[teamName]})</div>
        <div class="team-data">
            <div>Score: {teamData[teamName].score}</div>
            <div>Lives: {teamData[teamName].lives}</div>
        </div>
        <div class="player-data">
            {#each teammatesData as { name, score, deaths}, i}
                <div class="teammates-wrapper">
                    <span>
                        {name} ({playerRankings[name]})
                    </span>
                    <span>
                        score: {score}
                    </span>
                    <span>
                        deaths :{deaths}
                    </span>
                </div>
            {/each}
        </div>
      </div>
    {:else}
      <p>Loading data...</p>
    {/if}
  </main>

  
  <style>
    @import url('https://fonts.googleapis.com/css2?family=Bebas+Neue&display=swap');

    :root {
        font-family: 'Bebas Neue', sans-serif;
        font-size: 16px;
        line-height: 24px;
        font-weight: 800;
        font-size:16px;

        color: #ffffff;
        background-color: #f6f6f6;

        font-synthesis: none;
        text-rendering: optimizeLegibility;
        -webkit-font-smoothing: antialiased;
        -moz-osx-font-smoothing: grayscale;
        -webkit-text-size-adjust: 100%;
    }
    .team-data{
        display:flex;
        flex-direction:row;
        justify-content:space-between;
    }
    .teammates-wrapper{
        display:flex;
        flex-direction:column;
    }
    .player-data{
        margin-top: 30px;
        display:flex;
        flex-direction:row;
        justify-content:space-between;
    }
    main{
        margin:0;
        padding:0;
    }
    .title{
        display: flex;
        justify-content:center;
        margin-bottom:10px;
        font-size:32px;
    }
    .scoreboard-wrapper{
        background-color:black;
        width: 400px;
        padding: 10px;
    }

    :global(body) {
        padding:0;
        margin:0;
    }
  </style>
  