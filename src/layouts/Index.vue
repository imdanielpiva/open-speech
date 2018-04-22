<template>
  <div class="index-container-height">
  	<v-container justify-center fluid>
     <v-layout row wrap justify-center>
      <v-flex xl12 lg12 md12 sm12 xs12 justify-center>
        <v-btn icon @click="listen">
          <v-icon color="dark">keyboard_voice</v-icon>
        </v-btn>
      </v-flex>
      <v-flex dBlock xl12 lg12 md12 sm12 xs12 justify-center>
        <div>
          {{ message }}
        </div>
        <div>
          <v-progress-circular v-if="isListening" color="blue" indeterminate />
        </div>
      </v-flex>
     </v-layout> 
    </v-container>
  </div>
</template>

<script>
export default {
  data: () => ({
    message: '',
    voice: null,
    listening: false,
    synth: null,
    recognition: null,
    utterance: null
  }),
  methods: {
    listen() {
      this.listening = true;
      this.recognition.start();
    },
    speak() {
      this.utterance.text = this.message;

      if (this.voice) {
        this.utterance.voice = this.voice;
      }

      this.synth.speak(this.utterance);
    }
  },
  computed: {
    isListening() {
      return this.listening;
    }
  },
  created() {
    const SpeechRecognition = window.SpeechRecognition || window.webkitSpeechRecognition;

    this.recognition = new SpeechRecognition();
    this.utterance = new window.SpeechSynthesisUtterance();
    this.synth = window.speechSynthesis;

    this.synth.onvoiceschanged = () => {
      const voicesList = this.synth.getVoices();
      const { length: voices } = voicesList;

      for (let i = 0; i < voices; i += 1) {
        if (voicesList[i].lang === 'pt-BR') {
          this.voice = voicesList[i];
          console.log('found pt-BR voice');
          break;
        }
      }
    };

    this.recognition.lang = 'pt-BR';
    this.recognition.continuos = true;

    this.recognition.onresult = (evt) => {
      const [result] = evt.results;
      this.listening = false;
      this.message = result[0].transcript;

      setTimeout(this.speak, 500);
    };
  }
};
</script>

<style>
.index-container-height {
  height: calc(100vh - 64px);
}
</style>