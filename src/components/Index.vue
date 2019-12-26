<template>
  <div>
    Search Key Words:
    <input
      type="search"
      class="inp"
      v-model="keyword"
      placeholder="Keyword 1"
    />
    <input
      type="search"
      class="inp"
      v-model="keyword2"
      placeholder="Keyword 2"
    />
    <button @click="fetchWordsAPI()">
      Search
    </button>

    <hr />
    <button @click="markovIt()" v-if="words.length > 0">Generate Word</button>
    <button @click="markovMultiplier(10)" v-if="words.length > 0">
      Generate 10 Words
    </button>
    <button @click="markovMultiplier(100)" v-if="words.length > 0">
      Generate 100 Words
    </button>
    <table border="1">
      <tr>
        <th>Entry 1</th>
        <th>Entry 2</th>
        <th>Generated Words</th>
      </tr>
      <tr>
        <td valign="top">
          <div v-if="words.length > 0">
            <h3>Entry 1:</h3>
            <ol>
              <li v-for="(word, index) in words" :key="index">{{ word }}</li>
            </ol>
          </div>
        </td>
        <td valign="top">
          <div v-if="words2.length > 0">
            <h3>Entry 1:</h3>
            <ol>
              <li v-for="(word, index) in words2" :key="index">{{ word }}</li>
            </ol>
          </div>
        </td>

        <td valign="top">
          <div v-if="generated_names.length > 0">
            <ol>
              <li v-for="(names, index) in generated_names" :key="index">
                {{ names }}
              </li>
            </ol>
          </div>
        </td>
      </tr>
    </table>

    <div v-if="ngrams.length > 0">
      <h3>TriGrams:</h3>
      <ul>
        <li v-for="(ngram, index) in ngrams" :key="index">{{ ngram }}</li>
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
      keyword2: '',
      words: [],
      words2: [],
      combined_words: [],
      beginnings: [],
      beginnings2: [],
      ngrams: {},
      ngrams2: {},
      order: 3,
      generated_names: []
    };
  },
  methods: {
    fetchWordsAPI() {
      if (this.keyword === '') {
        return alert('Please input keyword 1');
      }
      if (this.keyword2 === '') {
        return alert('Please input keyword 2');
      }

      this.generated_names = [];

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

      axios
        .get(`https://api.datamuse.com/words?rel_trg=${this.keyword2}&max=50`)
        .then(response => {
          this.words2 = [];
          this.beginnings2 = [];
          response.data.forEach(data => {
            this.words2.push(data.word);
          });
          // this.words = response.data;
          this.generateGrams2();
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
    generateGrams2() {
      for (var j = 0; j < this.words2.length; j++) {
        var txt = this.words2[j];
        for (var i = 0; i < txt.length - this.order; i++) {
          var gram = txt.substring(i, i + this.order);

          if (i === 0) {
            this.beginnings2.push(gram);
          }

          if (!this.ngrams2[gram]) {
            this.ngrams2[gram] = [];
          }

          this.ngrams2[gram].push(txt.charAt(i + this.order));
          // this.ngrams.push(gram);
        }
      }
    },
    markovIt() {
      var currentGram = this.beginnings[
        Math.floor(Math.random() * this.beginnings.length)
      ];
      var currentGram2 = this.beginnings2[
        Math.floor(Math.random() * this.beginnings2.length)
      ];
      var result = currentGram;
      var result2 = currentGram2;

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
      for (var i2 = 0; i2 < 10; i2++) {
        var possibilities2 = this.ngrams2[currentGram2];
        if (!possibilities2) {
          break;
        }
        var next2 =
          possibilities2[Math.floor(Math.random() * possibilities2.length)];
        result2 += next2;
        var len2 = result2.length;
        currentGram2 = result2.substring(len2 - this.order, len2);
      }

      result = result.replace(/^\w/, c => c.toUpperCase());
      result2 = result2.replace(/^\w/, c => c.toUpperCase());

      this.generated_names.push(result + result2);
    },
    markovMultiplier(multiple) {
      for (var i = 0; i < multiple; i++) {
        this.markovIt();
      }
    }
  },
  mounted() {
    this.generateGrams();
  }
};
</script>

<style scoped></style>
