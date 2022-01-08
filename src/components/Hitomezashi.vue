<template>
<div class="container">

<h1>Hitomezashi Sashiko Stitching Patterns</h1>

<div class="row">
  <div class="col"></div>
  <div class="col-auto">
    Every second character is used for either the X or Y axis, only alphanumeric and
    space characters are considered. Vowels, even numbers and space characters
    means you start with a stitch on, otherwise you start with a stitch off in that
    position.
  </div>
  <div class="col"></div>
</div>

<div class="row">
  <div class="col"></div>

  <div class="col-md">
    <label for="phrase" class="visually-hidden">Phrase</label>
    <input type="text" class="form-control" id="phrase" v-model="phrase" placeholder="Some inspiring phrase">
  </div>
  <div class="col-auto">
    <button type="button" class="btn btn-primary mb-3" @click="generatePhrase()">Give me a phrase</button>
  </div>

  <div class="col"></div>
</div>

<div class="row">
  <div class="col-auto">
    <div class="hito-scene">
      <div v-for="(line, index) in stitches" :key="line">
        <div v-if="index == 0">
          <div class="hito-cell hito-x-stitch-off hito-y-stitch-off">
            <div class="hito-cell-label">&nbsp;</div>
          </div> <!-- Empty cell to line up with the next set of labels-->
          <div class="hito-cell hito-x-stitch-off hito-y-stitch-off" v-for="thing in x" :key="thing">
            <div class="hito-cell-label">{{ thing }}</div>
          </div>
        </div>
        <template
          v-for="(cell, cell_index) in line"
          :key="cell"
        >
          <template v-if="cell_index == 0">
            <div class="hito-cell hito-x-stitch-off hito-y-stitch-off">
              <div class="hito-cell-label">{{ y[index] }}</div>
            </div>
          </template>
          <div
            class="hito-cell"
            :class="{ 'hito-x-stitch-on': cell[0], 'hito-x-stitch-off': !cell[0], 'hito-y-stitch-on': cell[1], 'hito-y-stitch-off': !cell[1] }"
          />
        </template>
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

function shouldStitch(x) {
  return isVowel(x) || isEven(x) || x == ' '
}

function sanitizePhrase(phrase) {
  phrase = phrase.replace(/[^a-zA-Z0-9 ]/gi, '');
  return phrase.replace(/[ ]/gi, '\xa0');
}

export default {
  name: 'Hitomezashi',
  data () {
    return {
      phrase: "",
      stitches: [],
      x: [],
      y: [],
    }
  },
  watch: {
    phrase: _.debounce(function() {
      this.calculateStitches();
    }, 10),
  },
  created() {
    this.generatePhrase()
  },
  methods: {
    calculateStitches() {
      let stitches = [];
      const sanitized = sanitizePhrase(this.phrase);
      const length = Math.floor(sanitized.length / 2);
      let p1 = [];
      let p2 = [];
      for (let i = 0; i < length * 2; i++) {
        if (i % 2 == 0) {
          p1.push(sanitized[i])
        } else {
          p2.push(sanitized[i])
        }
      }
      for (let x = 0; x < length; x++) {
        stitches.push([]);
        const c1 = p1[x];
        const a = shouldStitch(c1);
        for (let y = 0; y < length; y++) {
          const c2 = p2[y];
          const b = shouldStitch(c2);
          stitches[x].push([y % 2 == 0 ? a : !a, x % 2 == 0 ? b : !b])
        }
      }
      this.stitches = stitches;
      this.y = p1;
      this.x = p2;
    },
    generatePhrase() {
      const idx = Math.floor(Math.random() * example_phrases.length);
      this.phrase = example_phrases[idx];
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
  padding: 0;
  margin: 0;
  aspect-ratio: 1;
  width: 30px;
  height: 30px;
  overflow-y: hidden;
}

.hito-cell-label {
   position: relative;
   top: 50%;
   font-weight: bold;
   text-align: center;
}

</style>
