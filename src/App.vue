<template>
  <v-app>
    <v-dialog
      v-model="dialog"
      fullscreen
      hide-overlay
      transition="dialog-bottom-transition"
    >
      <template v-slot:activator="{ on, attrs }">
        <v-btn
          fab
          id="setting-btn"
          elevation="0"
          color="grey lighten-3"
          v-bind="attrs"
          v-on="on"
          small
        >
          <v-icon dark>mdi-cog</v-icon>
        </v-btn>
      </template>
      <v-card>
        <v-toolbar color="white" elevation="0">
          <v-toolbar-title>Settings</v-toolbar-title>
          <v-spacer></v-spacer>
          <v-btn icon @click="dialog = false">
            <v-icon>mdi-close</v-icon>
          </v-btn>
        </v-toolbar>
        <v-list three-line subheader>
          <v-subheader>Random Corpus</v-subheader>
          <v-list-item>
            <v-range-slider
              v-model="generate"
              step="1"
              color="grey darken-4"
              track-color="grey"
              max="50"
              min="1"
              thumb-label="always"
              thumb-size="24"
              append-icon="mdi-check"
              @click:append="generateCorpus"
              hide-details
            >
              <template v-slot:thumb-label="props">
                {{ props.value }}
              </template></v-range-slider
            >
          </v-list-item>
          <v-subheader>Corpus Switcher</v-subheader>
          <v-list-item>
            <v-row no-gutters>
              <v-col
                v-for="i in corpusAll"
                :key="i"
                cols="1"
                style="padding: 4px"
              >
                <v-btn
                  depressed
                  :color="corpus.indexOf(i) > -1 ? 'grey darken-4' : 'grey'"
                  block
                  dark
                  @click="switchCorpus(i)"
                >
                  {{ i }}
                </v-btn>
              </v-col>
            </v-row>
          </v-list-item>
        </v-list>
      </v-card>
    </v-dialog>
    <v-container class="text-center">
      <v-card-text id="currentNum">
        {{ current > -1 ? current : "--" }}
      </v-card-text>
      <v-btn
        fab
        elevation="2"
        large
        color="grey lighten-5"
        @click="switchRandom()"
      >
        <v-icon dark>{{ interval ? "mdi-stop" : "mdi-play" }}</v-icon>
      </v-btn>
      <v-row no-gutters style="margin-top:48px">
        <v-col
          v-for="i in extract"
          :key="i"
          cols="2"
          style="padding: 4px; margin-bottom: 28px"
        >
          <v-btn depressed block color="grey lighten-3" id="extractNum">
            {{ i }}
          </v-btn>
        </v-col>
      </v-row>
    </v-container>
  </v-app>
</template>

<script>
export default {
  name: "App",

  data: () => ({
    dialog: false,
    generate: [1, 50],
    corpusAll: [],
    corpus: [],
    current: -1,
    interval: null,
    extract: [],
  }),

  mounted() {
    this.generateCorpus();
  },

  methods: {
    generateCorpus() {
      this.corpusAll = [];
      this.extract = [];
      for (let i = this.generate[0]; i <= this.generate[1]; i++) {
        this.corpusAll.push(i);
      }
      this.corpus = this.corpusAll.concat();
    },
    switchCorpus(i) {
      if (this.corpus.indexOf(i) > -1) {
        this.corpus.splice(this.corpus.indexOf(i), 1);
      } else {
        this.corpus.push(i);
      }
    },
    randomOnce() {
      this.current =
        this.corpus[Math.floor(Math.random() * this.corpus.length)];
    },
    switchRandom() {
      if (this.interval) {
        clearInterval(this.interval);
        this.interval = null;
        this.randomOnce();
        this.extract.push(this.current);
        this.switchCorpus(this.current);
      } else {
        this.interval = setInterval(() => {
          this.randomOnce();
        }, 50);
      }
    },
  },
};
</script>

<style>
*::-webkit-scrollbar {
  width: 0;
}

html,
body,
.v-application--wrap,
.container {
  height: 100vh;
  width: 100vw;
  background-color: #eee;
}

#setting-btn {
  position: fixed;
  top: 24px;
  right: 24px;
}

#currentNum {
  padding-top: 224px;
  padding-bottom: 224px;
  font-size: 128px;
  font-weight: bold;
  font-family: Consolas, Inconsolata, Courier, monospace;
}

#extractNum {
  font-size: 64px;
  font-weight: bold;
  font-family: Consolas, Inconsolata, Courier, monospace;
}
</style>