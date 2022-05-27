<template>
  <div class="forAll">
    <div class="initialConfigs" v-if="gameConfigs">
      Adicione os nomes dos jogadores
      <div class="players">
        <input @keyup.enter="addPlayers()" type="text" v-model="playerName" />
        <div>Players:</div>
        <div v-for="(player, i) in players" :key="player.name">
          Player {{ i }}:{{ player.name }}
        </div>
        <button @click="addPlayers()">Adicionar jogador.</button>
      </div>

      <button @click="confirmPlayers()">Confirmar.</button>
    </div>
    <div v-else>
      <div v-if="insertWord" class="initialConfigs">
        <div>Por favor,{{ players[0].name }}, insira uma palavra/frase!</div>
        <input type="password" v-model="textToGuess" />
        <button @click="defineNumberOfInputs()">Confirmar</button>
      </div>
      <div class="principalScreen" v-else>
        <div class="turno">Turno:{{ turnControl }}</div>

        <div class="vezJogador">
          Vez do jogador:
          <span :style="{ color: '#4caf50' }"
            >{{ players[playerTurn].name.toUpperCase() }}
          </span>
        </div>

        <div class="notInInstance">
          <div
            v-for="(letters, i) in lettersInputs"
            :key="i"
            class="letters"
            :style="{ borderStyle: letters.value === ' ' ? 'none' : 'solid' }"
          >
            <span v-show="letters.show">{{ letters.value.toUpperCase() }}</span>
          </div>
        </div>
        <div class="inTheWord">
          Letras usadas que estão na palavra:
          <div class="notInInstance">
            <div class="letters2" v-for="(letters, i) in inSentence" :key="i">
              {{ letters.toUpperCase() }}
            </div>
          </div>
        </div>
        <div class="inTheWord">
          Letras usadas que não estão na palavra:
          <div class="notInInstance">
            <div
              :style="{ backgroundColor: 'red' }"
              class="letters2"
              v-for="(letters, i) in notInSentence"
              :key="i"
            >
              {{ letters.toUpperCase() }}
            </div>
          </div>
        </div>

        <div class="inTheWord">
          CLIQUE EM UMA LETRA:
          <div class="notInInstance">
            <button
              class="letters3"
              v-for="letter in alphabet"
              :key="letter"
              @click="verifyLetter(letter)"
              :disabled="buttons"
            >
              {{ letter }}
            </button>
          </div>
        </div>

        <span>Insira a frase/palavra para tentar acertar!</span>
        <input type="text" v-model="theGuess" />
        <button @click="makeAGuess()">Tente acertar!</button>

        <div class="enforcado">
          <div>Tentativas erradas:{{ tentativas }}</div>
          <div v-show="tentativas">
            <span v-show="tentativas >= 7">_</span
            ><span v-show="tentativas >= 1">O</span
            ><span v-show="tentativas >= 7">_</span>
          </div>
          <div v-show="tentativas">
            <span v-show="tentativas >= 5">/</span>
            <span v-show="tentativas >= 2">|</span>
            <span v-show="tentativas >= 6">\</span>
          </div>
          <div v-show="tentativas">
            <span v-show="tentativas >= 3">/</span
            ><span v-show="tentativas >= 4">\</span>
          </div>
        </div>
        <div class="scores">
          <div >Round:{{ roundControl }}</div>

          <span>Escore Jogadores: </span>
          <span class="score" v-for="player in players" :key="player.name"
            >{{ player.name }}:{{ player.score }}</span
          >
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      roundControl: 0,
      turnControl: 0,
      playerTurn: 1,
      lettersInputs: [],
      textToGuess: "",
      confirm: false,
      buttons: false,
      players: [],
      playerName: "",
      gameConfigs: true,
      alphabet: [
        "A",
        "B",
        "C",
        "Ç",
        "D",
        "E",
        "F",
        "G",
        "H",
        "I",
        "J",
        "K",
        "L",
        "M",
        "N",
        "O",
        "P",
        "Q",
        "R",
        "S",
        "T",
        "U",
        "V",
        "W",
        "X",
        "I",
        "Z",
      ],
      notInSentence: [],
      inSentence: [],
      insertWord: true,
      theGuess: "",
    };
  },
  computed: {
    tentativas() {
      return this.notInSentence.length;
    },
  },
  methods: {
    defineNumberOfInputs() {
      if (this.textToGuess) {
        this.lettersInputs = [];
        this.confirm = true;
        const manageVetor = this.textToGuess.split("");
        manageVetor.forEach((element) => {
          this.lettersInputs.push({ value: element, show: false });
        });
      }
      this.insertWord = false;
    },
    verifyLetter(value) {
      this.buttons = true;
      let hasLetter = false;
      if (
        !this.lettersInputs.length ||
        this.notInSentence.indexOf(value) !== -1
      ) {
        this.buttons = false;
        return;
      }
      this.lettersInputs.forEach((element) => {
        if (element.value.toUpperCase() == value.toUpperCase()) {
          element.show = true;
          hasLetter = true;
        }
      });

      if (!hasLetter) {
        this.notInSentence.push(value);
      } else {
        this.inSentence.push(value);
      }
      this.alphabet.splice(this.alphabet.indexOf(value), 1);
      setTimeout(() => {
        this.verifyWin();
      }, 100);
    },
    verifyWin() {
      let showedWords = 0;
      this.lettersInputs.forEach((element) => {
        if (element.value === " ") showedWords++;
        else if (element.show) showedWords++;
      });

      if (showedWords == this.lettersInputs.length) {
        this.players[this.playerTurn].score++;
        this.restartGame();
      } else {
        if (this.tentativas == 7) {
          alert(`Morreu enforcado!, a palavra era "${this.textToGuess}"`);
          this.players[0].score++;
          this.restartGame();
        }
        this.turnControl++;
        this.managePlayers();
      }

      this.buttons = false;
    },
    addPlayers() {
      const removeSpaces = this.playerName.replace(/ /g, "");
      if (removeSpaces) {
        this.players.push({ name: this.playerName, score: 0 });
        this.playerName = "";
      }
    },
    confirmPlayers() {
      if (this.players.length) this.gameConfigs = false;
    },
    managePlayers() {
      const numberOfPlayers = this.players.length;
      if (this.playerTurn == numberOfPlayers - 1) {
        this.playerTurn = 1;
      } else {
        this.playerTurn++;
      }
    },
    manageWhoInsertTheWord() {
      const firstPlayer = this.players[0];
      this.players.splice(0, 1);
      this.players.push(firstPlayer);
    },
    restartGame() {
      this.manageWhoInsertTheWord();
      this.insertWord = true;
      this.turnControl = 0;
      this.playerTurn = 1;
      this.textToGuess = "";
      this.notInSentence = [];
      this.inSentence = [];
      this.roundControl++;
      this.alphabet = [
        "A",
        "B",
        "C",
        "Ç",
        "D",
        "E",
        "F",
        "G",
        "H",
        "I",
        "J",
        "K",
        "L",
        "M",
        "N",
        "O",
        "P",
        "Q",
        "R",
        "S",
        "T",
        "U",
        "V",
        "W",
        "X",
        "I",
        "Z",
      ];
    },
    makeAGuess() {
      if (this.textToGuess.toUpperCase() === this.theGuess.toUpperCase()) {
        alert(`${this.players[this.playerTurn].name} acertou, parabens!`);
        this.players[this.playerTurn].score++;
        this.restartGame();
      } else {
        this.turnControl++;
        this.managePlayers();
      }
      this.theGuess = "";
    },
  },
};
</script>

<style>
@import url("https://fonts.googleapis.com/css2?family=Press+Start+2P&display=swap");
body {
  font-family: "Press Start 2P", cursive;
}
.inTheWord {
  border-style: solid;
  padding: 15px;
  border-radius: 10px;
}
.forAll {
  display: flex;
  flex-direction: column;
  align-content: center;
  align-items: center;
  padding: 20px;
}
.turno {
  border-style: solid;
  padding: 10px;
  border-radius: 10px;
  margin: 10px;
}
.vezJogador {
  border-style: solid;
  padding: 10px;
  border-radius: 10px;
  margin: 10px;
}
.players {
  display: flex;
  flex-direction: column;
  gap: 10px;
}
.principalScreen {
  display: flex;
  flex-direction: column;
  border-style: solid;
  align-content: center;
  align-items: center;
  border-radius: 10px;
  padding: 20px;
  gap: 20px;
}
.initialConfigs {
  display: flex;
  flex-direction: column;
  width: 500px;
  align-content: center;
  min-height: 300px;
  border: solid;
  border-radius: 10px;
  margin: 10px;
  padding: 20px;
  gap: 50px;
}
input {
  font-family: "Press Start 2P", cursive;
  font-size: 17px;
  height: 30px;
  border-radius: 10px;
  align-content: center;
}
button {
  font-family: "Press Start 2P", cursive;
  height: 30px;
  border-radius: 10px;
  transition-duration: 0.4s;
}
button:hover {
  background-color: #4caf50;
  color: white;
}
.letters {
  display: flex;
  align-items: center;
  padding-left: 10px;
  height: 50px;
  width: 45px;
  font-size: 40px;
  border-style: solid;
  border-radius: 10px;
  margin-right: 5px;
}
.letters2 {
  display: flex;
  align-items: center;
  padding-left: 5px;
  height: 30px;
  width: 30px;
  font-size: 20px;
  border-style: solid;
  border-radius: 5px;
  margin-right: 5px;
  background-color: #4caf50;
}
.letters3{
    display: flex;
  align-items: center;
  padding-left: 5px;
  height: 50px;
  width: 50px;
  font-size: 20px;
  border-style: solid;
  border-radius: 10px;
  margin-right: 5px;
  background-color: #5082ee;
  border-width: 5px;
}
.letters3:hover{
  background-color: #bfc7d8;
 
}
.notInInstance {
  display: flex;
  flex-direction: row;
}
.enforcado {
  display: flex;
  flex-direction: column;
  align-content: center;
  align-items: center;
  border-style: solid;
  border-radius: 10px;
  padding: 20px;
}
.scores{
  padding: 20px;
  border-style: solid;
  border-radius: 10px;
}
.score{
  border-style: solid;
  padding: 10px;
  border-radius: 10px;
  margin: 5px;

}
</style>