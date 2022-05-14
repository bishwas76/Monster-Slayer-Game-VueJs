<template>
    <div class="main-game">
     <div id="game">
        <section id="monster" class="container">
            <h1>
                Monster Health
            </h1>
            <div class="healthbar">
                <div 
                    class="health__value"
                    :style="monsterHealthSyles">
                        <h2>{{ monsterHealth+'%' }}</h2>
                </div>
            </div>
        </section>
        <section id="player" class="container">
            <h1>
                My Health
            </h1>
            <div class="healthbar">
                <div 
                    class="health__value"
                    :style="playerHealthStyles">
                        <h2>{{ playerHealth+'%' }}</h2>
                </div>
            </div>
        </section>
        <section class="container" v-if="winner">
            <h2>
                Game Over !!!
            </h2>
            <h2 v-if="winner === 'lost'">You lost!</h2>
            <h2 v-else-if="winner === 'won'" >You Won!</h2>
            <h2 v-else>It's a draw!</h2>
            <button 
                @click="restartGame"
                :style="restartButtonStyle">
                    Restart The Game
            </button>
        </section>
        <section id="control-panal" v-else>
            <button 
                @click="attackMonster">
                    ATTACK
            </button>
            <button 
                @click="specialAttack"
                :disabled="mayUseSpecialAttack">
                    SPECIAL ATTACK
            </button>
            <button 
                @click="healPlayer">
                    HEAL
            </button>
            <button 
                @click="surrenderGame">
                    SURRENDER
            </button>
        </section>
    </div>
    <div class="game-log">
         <section id="log" class="container">
            <h1>Battle Log</h1>
            <ul>
                <!--To list all the battle log list dynamically in game using v-for directive-->
                <li v-for="logMessage in logMessages" :key="logMessage">
                    <span 
                        :class="{
                            'log--monster': logMessage.actionBy==='monster',
                            'log--player': logMessage.actionBy==='player'
                        }">
                            {{ logMessage.actionBy === 'player'? 'Player': 'Monster' }}
                    </span>
                    <span v-if="logMessage.actionType === 'attack' || logMessage.actionType ==='special-attack'">
                         {{ " "+logMessage.actionType }}s and deals
                        <span class="log--damage">
                            {{ logMessage.actionValue }} damage
                        </span>
                    </span>
                    <span v-else>
                        heals health by
                        <span class="log--heal">
                            {{ logMessage.actionValue }} life
                        </span>
                    </span>
                </li>
            </ul>
        </section>
    </div>       
    </div>
</template>

<script>
export default {
    data() {
        return {
            monsterHealth: 100,
            playerHealth: 100,
            currentRound: 0,
            winner: null,
            logMessages: [],
        };
    },
    //Use of computed property in order to dynamically change 
    // or bind the style of html element form
    // as well as to bind the button element disable property.
    computed: {
        monsterHealthSyles() {
            if (this.monsterHealth < 0){
                this.monsterHealth = 0
                return {
                    width: this.monsterHealth+'%',
                }
            }
            return {
                width: this.monsterHealth+'%'
            }
        },
        playerHealthStyles() {
            if (this.playerHealth < 0){
                this.playerHealth = 0
                return {
                    width: this.playerHealth+'%',
                }
            }
            return {
                width: this.playerHealth+'%'
            }
        },
        mayUseSpecialAttack(){
            //player will be only able to use the special attack feature after 3 rounds per use.
            return ((this.currentRound % 3) !== 0)
        },
        restartButtonStyle() {
            return {
                background: '#B4341D',
            }
        }
    },
    //Use of watcher for checking player and monster health 
    //to check if who won, lost, or the game was a draw
    watch: {
        playerHealth(value) {
            if(value <= 0 && this.monsterHealth <= 0){
                this.winner = 'draw'
            }else if(value<=0){
                this.winner = 'lost'
            }
        },
        monsterHealth(value) {
            if(value <= 0 && this.playerHealth <= 0){
               this.winner = 'draw'
            }else if(value<=0){
                this.winner = 'won'
            }
        },
    },
    methods: {
        getRandomvalue(min, max){
            return Math.floor(Math.random()*(max-min))+min
        },
        attackMonster(){
            this.currentRound++
            const attackValue = this.getRandomvalue(5, 12)
            this.monsterHealth -= attackValue
            this.gameLogs('monster', 'attack', attackValue)
            this.attackPlayer()
        },
        attackPlayer(){
            const attackValue = this.getRandomvalue(8, 15)
            this.playerHealth -= attackValue
            this.gameLogs('player', 'attack', attackValue)
        },
        specialAttack(){
            this.currentRound++
            const attackValue = this.getRandomvalue(10, 25)
            this.monsterHealth -= attackValue
            this.gameLogs('player', 'special-attack', attackValue)
            this.attackPlayer()
        },
        healPlayer() {
            this.currentRound++
            const healValue = this.getRandomvalue(8, 20)
            if(this.playerHealth+healValue > 100){
                this.playerHealth =100
            }else{
                this.playerHealth += healValue
            }
            this.gameLogs('player', 'heal', healValue)
            this.attackPlayer()
        },
        surrenderGame() {
            this.winner = 'lost'
        },
        restartGame(){
            this.monsterHealth = 100
            this.playerHealth = 100
            this.currentRound = 0
            this.winner = null
            this.logMessages = []         
        },
        gameLogs(whoDid, whatType, value) {
            this.logMessages.unshift({
                actionBy: whoDid,
                actionType: whatType,
                actionValue: value
            })
        }
    },
}
</script>

<style>
.main-game{
    height: auto;
    display: flex;
    flex-wrap: wrap;
    flex-direction: row;
}
.game-log{
    width: 20rem;
    margin: 0px 3rem;
}
#log{
    position: fixed;
    height: 50%;
    overflow: auto;
}
section {
    width:100%;
    max-width: 500px;
    margin: auto;
}
.container {
    text-align: center;
    padding: 10px;
    margin: 30px auto;
    border-radius: 12px;
    box-shadow: 0 2px 8px rgb(0, 0, 0, 0.35);;
}
.healthbar{
    width: 100%;
    height: 40px;
    margin: 20px 0;
    background-color: rgb(254, 220, 220);
}
.health__value{
    width: 100%;
    height: 100%;
    background-color: #00a876;
    color: whitesmoke;
}
#control-panal{
    display: flex;
    flex-wrap: wrap;
    flex-direction: row;
    justify-content: center;
}
button {
    background-color: rgb(150, 150, 229);
    color: whitesmoke;
    border: none;
    padding: 20px 30px;
    margin: 15px;
    width: 200px;
    cursor: pointer;
    border-radius: 12px;
    box-shadow: 1px 1px 4px rgba(0, 0, 0, 0.26);
    font-weight: bold;
}
button:hover,
button:active {
  background-color: #af0a78;
  border-color: #af0a78;
  box-shadow: 1px 1px 8px rgba(0, 0, 0, 0.26);
}
button:disabled {
  background-color: #ccc;
  border-color: #ccc;
  box-shadow: none;
  color: #3f3f3f;
  cursor: not-allowed;
}
h1 {
  font-weight: bold;
  color: black;
}
#log ul {
  list-style: none;
  margin: 0;
  padding: 0;
}

#log li {
  margin: 0.5rem 0;
}

.log--player {
  color: #7700ff;
}

.log--monster {
  color: #da8d00;
}

.log--damage {
  color: red;
}

.log--heal {
  color: green;
}
</style>