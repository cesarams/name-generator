<template>
  <div>
<<<<<<< Updated upstream
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
=======
    <input type="text" v-model="first_name" placeholder="First name" />
    <input type="text" v-model="last_name" placeholder="Last name" />
    <button @click="generateNames()">Generate Names</button>
    <ol>
      <li v-for="(word, index) in combined_words" :key="index">{{ word }}</li>
    </ol>
>>>>>>> Stashed changes
  </div>
</template>

<script>
// import axios from 'axios';
import 'core-js'; // <- at the top of your entry point
export default {
  name: 'Index',
  props: {},
  data() {
    return {
      brand_names: ['Airtek', 'Accort'],
      suffix: ['Logistics', 'Services'],
      first_name: 'Karl',
      last_name: 'Cayanan',
      combined_words: [],
      syllables: [],
      order: 3
    };
  },
  methods: {
    generateSyllables() {
      const syllableRegex = /[^aeiouy]*[aeiouy]+(?:[^aeiouy]*$|[^aeiouy](?=[^aeiouy]))?/gi;
      this.brand_names.forEach(name => {
        var holder = name.match(syllableRegex);
        holder.forEach(word => {
          this.syllables.push(word);
        });
      });
    },
    generateNames() {
      this.combined_words = [];
      this.syllables.forEach(first => {
        this.syllables.forEach(second => {
          first = first.replace(/^\w/, c => c.toUpperCase());
          second = second.replace(/^\w/, c => c.toUpperCase());
          if (first === second)
            console.log(`first: ${first}, second: ${second}`);
          else {
            second = second.replace(/^\w/, c => c.toLowerCase());
            this.suffix.forEach(suffix => {
              this.combined_words.push(
                `${this.first_name}'s ${first}${second} ${suffix}`
              );
              this.combined_words.push(
                `${this.last_name}'s ${first}${second} ${suffix}`
              );
            });
          }
        });
      });
      this.combined_words.sort();
    }
  },
  mounted() {
    this.generateSyllables();
  }
};
</script>

<style scoped></style>
