<template>
<div class="container">

<h1>Hitomezashi Sashiko Stitching Patterns</h1>

<div class="row">
  <div class="col"></div>
  <div class="col-auto">
    The pattern is generated based on the shortest phrase
  </div>
  <div class="col"></div>
</div>

<div class="row">
  <div class="col"></div>

  <div class="col-auto">
    <label for="phrase1" class="visually-hidden">Phrase 1</label>
    <input type="text" class="form-control" id="phrase1" v-model="phrase1" placeholder="Some inspiring phrase">
  </div>
  <div class="col-auto">
    <label for="phrase2" class="visually-hidden">Phrase 2</label>
    <input type="text" class="form-control" id="phrase2" v-model="phrase2" placeholder="Some inspiring phrase">
  </div>
  <div class="col-auto">
    <button type="button" class="btn btn-primary mb-3" @click="generatePhrases()">Give me some phrases</button>
  </div>

  <div class="col"></div>
</div>


  <div class="row">
    <div class="col-auto">
      <div class="hito-scene">
        <div v-for="(line, index) in stitches" :key="index">
          <div
            v-for="cell in line"
            :key="cell"
            class="hito-cell"
            :class="{ 'hito-x-stitch-on': cell[0], 'hito-x-stitch-off': !cell[0], 'hito-y-stitch-on': cell[1], 'hito-y-stitch-off': !cell[1] }"
          />
        </div>
      </div>
    </div>
  </div>
</div>
</template>

<script>

import _ from 'lodash';

const example_phrases = [
  "Yes sir, I need a weapon.",
  "Sir, finishing this fight.",
  "Thought I'd try shooting my way out. Mix things up a little.",
  "Something was wrong even before we left the Dawn.",
  "To give the covenant back their bomb.",
  "Are any of the ship defenses online?",
  "I thought we had a truce with the Covenant.",
  "She said that to me once... about being a machine.",
  "This fight... is finished.",
  "You told me there wouldn't be any cameras.",
  "We need to get off this ship.",
];

function isVowel(x) {
  const y = x.toLowerCase();
  return y === "a" || y === "e" || y === "i" || y === "o" || y === "u";
}

function isEven(x) {
  const y = parseInt(x);
  if (isNaN(y)) {
    return false;
  }
  return y % 2 === 0
}

function sanitizePhrase(phrase) {
  return phrase.replace(/[^a-zA-Z0-9]/gi, '');
}

export default {
  name: 'Hitomezashi',
  data () {
    return {
      phrase1: "",
      phrase2: "",
      stitches: [],
    }
  },
  watch: {
    phrase1: _.debounce(function() {
      this.calculateStitches();
    }, 100),
    phrase2: _.debounce(function() {
      this.calculateStitches();
    }, 100)
  },
  created() {
    this.generatePhrases()
  },
  methods: {
    calculateStitches() {
      let stitches = [];
      const phrase1 = sanitizePhrase(this.phrase1);
      const phrase2 = sanitizePhrase(this.phrase2);
      const length = Math.min(phrase1.length, phrase2.length);
      for (let x = 0; x < length; x++) {
        stitches.push([]);
        const c1 = phrase1[x];
        const a = isVowel(c1) || isEven(c1);
        for (let y = 0; y < length; y++) {
          const c2 = phrase2[y];
          const b = isVowel(c2) || isEven(c2);
          stitches[x].push([y % 2 == 0 ? a : !a, x % 2 == 0 ? b : !b])
        }
      }
      this.stitches = stitches;
    },
    generatePhrases() {
      const first = Math.floor(Math.random() * example_phrases.length);
      let second;
      for (;;) {
        second = Math.floor(Math.random() * example_phrases.length);
        if (second != first) {
          break;
        }
      }
      this.phrase1 = example_phrases[first];
      this.phrase2 = example_phrases[second];

      this.calculateStitches();
    }
  }
}
</script>

<style scoped>
.hito-x-stitch-on {
  border-bottom: 3px solid orangered;
}
.hito-x-stitch-off {
  border-bottom: 3px dashed lightgray;
}
.hito-y-stitch-on {
  border-right: 3px solid orangered;

}
.hito-y-stitch-off {
  border-right: 3px dashed lightgray;
}

.hito-scene {
  overflow-x: auto;
  white-space: nowrap;
  line-height: 0;
  width: auto;
  margin: auto;
}

.hito-cell {
  display: inline-block;
  padding: 0 !important;
  margin: 0 !important;
  aspect-ratio: 1;
  width: 30px;
  height: 30px;
}

</style>
