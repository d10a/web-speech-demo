<script setup lang="ts">
import { onBeforeMount, onMounted, ref } from 'vue'


const supportMsg = ref('')
const speechSynthesisVoices = ref<SpeechSynthesisVoice[]>()
const selectedVoice = ref<SpeechSynthesisVoice>()
const volume = ref(0.5)
const rate = ref(1.0)
const pitch = ref(1.0)
const text = ref('')
/**
 * Fetch voices form user's system
 */
onMounted(() => { })

text.value = "Jeune ange aux doux regards, à la douce parole,Un instant près de vous je suis venu m'asseoir,Et, l'orage apaisé, comme l'oiseau s'envole,Mon bonheur s'en alla, n'ayant duré qu'un soir."

const loadSystemVoices = () => {
  //@ts-ignore
  speechSynthesisVoices.value = speechSynthesis.getVoices()

  speechSynthesisVoices.value.forEach((voice: SpeechSynthesisVoice) => {
    if (voice.default) {
      selectedVoice.value = voice
    }
    return voice;
  })
}

setTimeout(() => {
  loadSystemVoices()
}, 2000)
window.speechSynthesis.onvoiceschanged = loadSystemVoices();


/*
 * Check for browser support
 */
if (typeof speechSynthesis === 'undefined') {
  supportMsg.value = 'Sorry your browser <strong>does not support</strong> speech synthesis.<br>Try this in <a href="https://www.google.co.uk/intl/en/chrome/browser/canary.html">Chrome Canary</a>.';

} else {
  supportMsg.value = 'Your browser <strong>supports</strong> speech synthesis.';
}


const speak = () => {
  try {
    if (speechSynthesis.speaking) {
      speechSynthesis.cancel()
    }
    console.log("Speaking...")
    // Create a new instance of SpeechSynthesisUtterance.
    var msg = new SpeechSynthesisUtterance();

    // Set the text.
    msg.text = text.value;
    msg.volume = volume.value;
    msg.rate = rate.value;
    msg.pitch = pitch.value;
    msg.voice = selectedVoice.value;
    console.log(msg)

    // Queue this utterance.
    speechSynthesis.speak(msg);
  } catch (error) {
    console.error(error)
  }
}
</script>

<template>
  <div id="page-wrapper">
    <h1>Web Speech Synthesis Demo (Text-to-Speech)</h1>

    <p id="msg">{{ supportMsg }}</p>

    <textarea type="text" name="speech-msg" id="speech-msg" x-webkit-speech v-model="text"></textarea>

    <div v-if="speechSynthesisVoices.length > 0" class="option">
      <label for="voice">Voice</label>
      <select name="voice" id="voice">
        <option v-for="voice in speechSynthesisVoices" v-bind:value="voice.voiceURI">
          {{ voice.name }}
        </option>
      </select>
    </div>
    <div class="option">
      <label for="volume">Volume</label>
      <input type="range" min="0" max="1" step="0.1" name="volume" id="volume" v-model="volume">
    </div>
    <div class="option">
      <label for="rate">Rate</label>
      <input type="range" min="0.1" max="10" step="0.1" name="rate" id="rate" v-model="rate">
    </div>
    <div class="option">
      <label for="pitch">Pitch</label>
      <input type="range" min="0" max="2" step="0.1" name="pitch" id="pitch" v-model="pitch">
    </div>

    <button id="speak" v-on:click="speak">Speak</button>

  </div>
</template>

<style scoped>
#page-wrapper {
  width: 640px;
  background: #FFFFFF;
  padding: 1em;
  margin: 1em auto;
  border-top: 5px solid #69c773;
  box-shadow: 0 2px 10px rgba(0, 0, 0, 0.8);
}

h1 {
  margin-top: 0;
}

#msg {
  font-size: 0.9em;
  line-height: 1.4em;
}

#msg.not-supported strong {
  color: #CC0000;
}

textarea {
  width: 80%;
  padding: 0.5em;
  font-size: 1.2em;
  border-radius: 3px;
  border: 1px solid #D9D9D9;
  box-shadow: 0 2px 3px rgba(0, 0, 0, 0.1) inset;
}

input[type="range"] {
  width: 300px;
}

label {
  display: inline-block;
  float: left;
  width: 150px;
}

.option {
  margin: 1em 0;
}

button {
  display: inline-block;
  border-radius: 3px;
  border: none;
  font-size: 0.9rem;
  padding: 0.5rem 0.8em;
  background: #69c773;
  border-bottom: 1px solid #498b50;
  color: white;
  -webkit-font-smoothing: antialiased;
  font-weight: bold;
  margin: 0;
  width: 100%;
  text-align: center;
}

button:hover,
button:focus {
  opacity: 0.75;
  cursor: pointer;
}

button:active {
  opacity: 1;
  box-shadow: 0 -3px 10px rgba(0, 0, 0, 0.1) inset;
}
</style>
