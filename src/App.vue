<script setup lang="ts">
import { ref } from "vue";
import * as data from "./models/data";
import { SecretWord } from "./models/splitWord";
import { IpickedWord } from "./models/IpickedWord";

const characters = data.characters.map((character) => ({
  letter: character,
  guessed: false,
}));

const words = data.words;
const pickedWord = ref<IpickedWord>();
const guessCount = ref(8);
const hangManWord = ref<SecretWord>();

function getRandomWordIndex(words: any) {
  const randomIndex = Math.floor(Math.random() * words.length);
  pickedWord.value = words[randomIndex];
  console.log("pickedWord:", pickedWord.value);
  if (pickedWord.value) {
    hangManWord.value = new SecretWord(randomIndex, pickedWord.value.word);
  }
  console.log("hangManWord:", hangManWord.value);
  console.log("pickedword:", pickedWord.value);
}

getRandomWordIndex(words);

const guessedBtn = (character: string) => {
  if (pickedWord.value) {
    let foundMatch = false;

    for (let i = 0; i < pickedWord.value.word.length; i++) {
      if (pickedWord.value.word[i] === character) {
        hangManWord.value.showed[i].showed = true;
        foundMatch = true;
      }
    }

    if (!foundMatch) {
      guessCount.value--;
    }

    characters.find((char) => char.letter === character).guessed = true;
  }
};
</script>

<template>
  <h1>Hangman</h1>
  <p>Gissningar kvar: {{ guessCount }}</p>
  <div class="hill"></div>

  <div class="letter-container">
    <div v-for="(letter, index) in hangManWord?.word" class="letter">
      <p v-if="hangManWord?.showed && hangManWord.showed[index]?.showed">{{ letter }}</p>
    </div>
  </div>

  <div v-if="guessCount > 0" class="button-container">
    <div class="secret-word-container"></div>
    <button
      v-for="(character) in characters"
      :key="character.letter"
      @click="guessedBtn(character.letter)"
      :disabled="character.guessed"
    >
      {{ character.letter }}
    </button>
  </div>
  <div v-else>
    <h2>Gubben är hängd 😥</h2>
  </div>
</template>




<style scoped>
.hill {
  padding: 2rem;
  border-top-right-radius: 50%;
  border-top-left-radius: 50%;
  border: 1px solid black;
  background-color: green;
  width: 500px;
  margin: 0 auto;
}

.button-container {
  display: flex;
  flex-wrap: wrap;
  max-width: 1000px;
}

.letter-container {
  display:flex;
  flex-direction: row;
  justify-content: space-around;
}

.letter {
  border-bottom: 1px solid white;
  margin: 1rem;
  width: 50px;
  height: 50px;
}
</style>
