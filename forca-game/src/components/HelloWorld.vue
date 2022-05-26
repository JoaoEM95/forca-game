<template>
  <div>
    <input type="text" v-model="textToGuess" :disabled="confirm" />
    <button @click="defineNumberOfInputs()" :disabled="confirm">
      Confirmar
    </button>

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
    Letras usadas que estão na palavra:
    <div class="notInInstance">
      <div class="letters2" v-for="(letters, i) in inSentence" :key="i">
        {{ letters.toUpperCase() }}
      </div>
    </div>
    Letras usadas que não estão na palavra:
    <div class="notInInstance">
      <div class="letters2" v-for="(letters, i) in notInSentence" :key="i">
        {{ letters.toUpperCase() }}
      </div>
    </div>
    Letras não usadas:
    <div class="notInInstance">
      <button
        class="letters2"
        v-for="letter in alphabet"
        :key="letter"
        @click="verifyLetter(letter)"
        :disabled="buttons"
      >
        {{ letter }}
      </button>
    </div>
    <div>Tentativas erradas:{{ tentativas }}</div>
    <div v-show="tentativas"><span v-show="tentativas>=7">_</span><span v-show="tentativas>=1">O</span><span v-show="tentativas>=7">_</span></div>
    <div v-show="tentativas"><span v-show="tentativas>=5">/</span> <span v-show="tentativas>=2">|</span> <span v-show="tentativas>=6">\</span></div>
    <div v-show="tentativas"><span v-show="tentativas>=3" >/</span><span v-show="tentativas>=4">\</span></div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      lettersInputs: [],
      textToGuess: "",
      confirm: false,
      buttons: false,
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
      this.textToGuess = "";
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

      if (showedWords == this.lettersInputs.length) alert("Venceu, parabéns!");
      this.buttons = false;
    },
  },
};
</script>

<style>
.letters {
  height: 50px;
  width: 50px;
  font-size: 40px;
  border-style: solid;
  border-radius: 10px;
  margin-right: 5px\;;
}
.letters2 {
  height: 30px;
  width: 30px;
  font-size: 20px;
  border-style: solid;
  border-radius: 5px;
}
.notInInstance {
  display: flex;
  flex-direction: row;
}
</style>