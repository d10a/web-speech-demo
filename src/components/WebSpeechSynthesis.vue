<script setup lang="ts">
import { onBeforeMount, onMounted, ref, watch } from 'vue'


const supportMsg = ref('')
const speechSynthesisVoices = ref<SpeechSynthesisVoice[]>()
const selectedVoice = ref<SpeechSynthesisVoice>()

const text = ref('')

const getImageUrl = (path: string) => {
  return new URL(path, import.meta.url).href
}

text.value = "Jeune ange aux doux regards, à la douce parole,Un instant près de vous je suis venu m'asseoir,Et, l'orage apaisé, comme l'oiseau s'envole,Mon bonheur s'en alla, n'ayant duré qu'un soir."

const loadSystemVoices = () => {
  //@ts-ignore
  speechSynthesisVoices.value = speechSynthesis.getVoices().filter(voice => {
    return voice.lang.match(/^fr/)
  })


}

// const setVoice = (selectedVoice) => {
//   speechSynthesisVoices.value.forEach((voice: SpeechSynthesisVoice) => {
//     if (voice.voiceURI === ) {
//       selectedVoice.value = voice
//     }
//     return voice;
//   })
// }

setTimeout(() => {
  loadSystemVoices()
}, 2000)
speechSynthesis.onvoiceschanged = loadSystemVoices();


/*
 * Check for browser support
 */
if (typeof speechSynthesis === 'undefined') {
  supportMsg.value = 'Sorry your browser <strong>does not support</strong> speech synthesis.<br>Try this in <a href="https://www.google.co.uk/intl/en/chrome/browser/canary.html">Chrome Canary</a>.';

} else {
  supportMsg.value = '';
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
    msg.volume = 0.5;
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
    <h1>Web Speech Synthesis Demo</h1>
    <p>(Text-to-Speech)</p>

    <p id="msg">{{ supportMsg }}</p>

    <textarea type="text" name="speech-msg" id="speech-msg" x-webkit-speech v-model="text">
    </textarea>

    <div v-if="speechSynthesisVoices.length > 0" class="option">
      <label for="voice">Voice</label>
      <select name="voice" id="voice" v-model="selectedVoice">
        <option v-for="voice in speechSynthesisVoices" v-bind:value="voice">
          {{ voice.name }} - {{ voice.lang }}
        </option>
      </select>
    </div>
    <button id="play-button" v-on:click="speak">
      <img :src="getImageUrl('/assets/images/play-button.svg')" alt="Play button" />
    </button>

  </div>
</template>

<style scoped>
#page-wrapper {
  font-size: 1.6rem;
  width: 60vw;
  background: transparent;
  border-radius: 10px;
  padding: 3em;
  margin: 1em auto;
  color: #FFFFFF;
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
  width: 50vw;
  min-height: 50vh;
  padding: 0.5em;
  font-size: 1.2em;
  border-radius: 10px;
  border: 1px solid #D9D9D9;
}

button {
  display: inline-block;
  border-radius: 3px;
  border: none;
  font-size: 0.9rem;
  padding: 0.8em;
  background: #69c773;
  color: white;
  font-weight: bold;
  margin: 0;
  width: 100%;
  text-align: center;
}

#play-button:hover,
#play-button:focus {
  opacity: 0.75;
  cursor: pointer;
}

#play-button:active {
  opacity: 1;
  box-shadow: 0 -3px 10px rgba(0, 0, 0, 0.1) inset;
}

#play-button {
  width: 5.5rem;
}
</style>
