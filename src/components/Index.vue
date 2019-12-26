<template>
  <div>
    Generate words
    <br />
    <input type="search" v-model="keyword" placeholder="Keyword 1" />
    <br />
    <button @click="fetchWordsAPI()">
      Search
    </button>

    <hr />
    <!-- <div v-if="words.length > 0">
      <h3>Entry 1:</h3>
      <ul>
        <li v-for="(word, index) in words" :key="index">{{ word }}</li>
      </ul>
    </div> -->

    <!-- <div v-if="words.length > 0">
      <h3>Beginnings :</h3>
      <ul>
        <li v-for="(begin, index) in beginnings" :key="index">{{ begin }}</li>
      </ul>
    </div> -->

    <button @click="markovIt()">Click</button>
    <div v-if="ngrams.length > 0">
      <h3>TriGrams:</h3>
      <ul>
        <li v-for="(ngram, index) in ngrams" :key="index">{{ ngram }}</li>
      </ul>
    </div>
    <div v-if="generated_names.length > 0">
      <h3>Generated Names:</h3>
      <ul>
        <li v-for="(names, index) in generated_names" :key="index">
          {{ names }}
        </li>
      </ul>
    </div>
  </div>
</template>

<script>
import axios from 'axios';
import 'core-js'; // <- at the top of your entry point
export default {
  name: 'Index',
  props: {
    msg: String
  },
  data() {
    return {
      keyword: '',
      words: [
        'coding',
        'lodi',
        'business',
        'charity',
        'makeover',
        'financial',
        'group',
        'manpower'
      ],
      unknown_1: [],
      combined_words: [],
      beginnings: [],
      order: 2,
      ngrams: {},
      generated_names: []
    };
  },
  methods: {
    fetchWordsAPI() {
      if (this.keyword === '') {
        return alert('Please input keyword 1');
      }

      // axios
      //   .get(`https://similarwords.p.rapidapi.com/moar?query=${this.keyword}`, {
      //     headers: {
      //       'x-rapidapi-host': 'similarwords.p.rapidapi.com',
      //       'x-rapidapi-key':
      //         '5b9b6568a5msh9909c9bccdbee9fp1c2d08jsne521340b9a2a'
      //     }
      //   })
      //   .then(response => {
      //     if (response.data.unknown.length > 0)
      //       return alert(`Unknown Entry: ${this.keyword}`);
      //     this.words = response.data.result;
      //     this.generateGrams();
      //   })
      //   .catch(err => {
      //     console.log(err);
      //   });
      axios
        .get(`https://api.datamuse.com/words?rel_trg=${this.keyword}&max=50`)
        .then(response => {
          this.words = [];
          this.beginnings = [];
          response.data.forEach(data => {
            this.words.push(data.word);
          });
          // this.words = response.data;
          this.generateGrams();
        })
        .catch(err => {
          console.log(err);
        });
    },

    generateGrams() {
      for (var j = 0; j < this.words.length; j++) {
        var txt = this.words[j];
        for (var i = 0; i < txt.length - this.order; i++) {
          var gram = txt.substring(i, i + this.order);

          if (i === 0) {
            this.beginnings.push(gram);
          }

          if (!this.ngrams[gram]) {
            this.ngrams[gram] = [];
          }

          this.ngrams[gram].push(txt.charAt(i + this.order));
          // this.ngrams.push(gram);
        }
      }
    },
    markovIt() {
      var currentGram = this.beginnings[
        Math.floor(Math.random() * this.beginnings.length)
      ];
      console.log(currentGram);
      var result = currentGram;

      for (var i = 0; i < 10; i++) {
        var possibilities = this.ngrams[currentGram];
        if (!possibilities) {
          break;
        }
        var next =
          possibilities[Math.floor(Math.random() * possibilities.length)];
        result += next;
        var len = result.length;
        currentGram = result.substring(len - this.order, len);
      }

      this.generated_names.push(result);
    }
  },
  mounted() {
    this.generateGrams();
  }
};
</script>

<style scoped></style>
