<template>
  <div>
    <select v-model="category">
      <option :value="suf.name" v-for="suf in suffix" :key="suf.name">{{
        suf.name
      }}</option>
    </select>
    <input type="text" v-model="first_name" placeholder="First name" />
    <input type="text" v-model="last_name" placeholder="Last name" />
    <button @click="generateNames()">Generate Names</button>
    <ol>
      <li v-for="(word, index) in combined_words" :key="index">{{ word }}</li>
    </ol>
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
      brand_names: [
        'Airtek',
        'Accort',
        'Alpha',
        'Real',
        'Homes',
        'Realty',
        'Villas',
        'Housing',
        'Estate',
        'Max',
        'Move'
      ],
      suffix2: ['Logistics', 'Services'],
      suffix: [
        { name: 'Food', items: ['FoodServices', 'Restaurant', 'Shop', 'Cafe'] },
        { name: 'Industry', items: ['Manpower Services', 'Services'] },
        {
          name: 'Business',
          items: [
            'Company',
            'Comp',
            'Limited',
            'LTD.',
            'Corporation',
            'Corp.',
            'LLC.'
          ]
        },
        {
          name: 'Real Estate',
          items: [
            'Real Estate',
            'Real Estate Group',
            'Real Estate Agency',
            'Real Estate Team',
            'Real Estate Services',
            'Realtors',
            'Real Estate Agent',
            'Estate Agent',
            'Realtor'
          ]
        }
      ],
      first_name: 'Karl',
      last_name: 'Cayanan',
      combined_words: [],
      syllables: [],
      category: '',
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
      if (this.category === '') return console.log('error');

      this.combined_words = [];
      this.syllables.forEach(first => {
        this.syllables.forEach(second => {
          first = first.replace(/^\w/, c => c.toUpperCase());
          second = second.replace(/^\w/, c => c.toUpperCase());
          if (first === second)
            console.log(`first: ${first}, second: ${second}`);
          else {
            second = second.replace(/^\w/, c => c.toLowerCase());

            var suf = this.suffix.filter(value => value.name == this.category);

            suf[0].items.forEach(suffix => {
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
