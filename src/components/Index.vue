<template>
  <div>
    <select v-model="s_industry" aria-placeholder="Select a industryt">
      <option value="" disabled>Select Industry</option>
      <option
        v-for="i_industry in Industry"
        :key="i_industry"
        :value="i_industry"
        >{{ i_industry }}</option
      >
    </select>
    <button @click="fetchWordsAPI(s_industry)">Search</button>
    <hr />
    <table width="100%" border="1">
      <tr>
        <th>Original Code</th>
        <th v-if="s_industry === '' || related_words.length === 0">
          Related Words
        </th>
        <th v-else>Related Words for {{ s_industry }}</th>
        <th>Industry + Related Words</th>
        <th>Mix of Prefix or Suffix</th>
      </tr>
      <tr>
        <td valign="top">
          <div
            style="padding:5px;"
            id="output"
            v-for="(name, index) in names"
            :key="index"
          >
            {{ name }}
          </div>
        </td>
        <td valign="top">
          <p v-for="(r_word, index) in related_words" :key="index">
            {{ r_word }}
          </p>
        </td>
        <td valign="top">
          <p v-for="(ir_word, index) in industry_related_words" :key="index">
            {{ ir_word }}
          </p>
        </td>
      </tr>
    </table>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  data() {
    return {
      Industry: ['Lawyer', 'Plumber', 'Consulant', 'Construction'],
      IndustryWordCombination: [
        ['Industry', 'Word'],
        ['Word', 'Industry']
      ],
      MixCombination: [
        ['Prefix', 'Industry'],
        ['Industry', 'Suffix'],
        ['Prefix', 'Industry', 'Suffix']
      ],
      Components: [
        {
          Founder: [
            { FirstName: 'Adam', LastName: 'Seabrook' },
            { FirstName: 'Eddie', LastName: 'Machaalani' },
            { FirstName: 'Todd', LastName: 'Farrell' },
            { FirstName: 'Sally', LastName: 'Smith' }
          ],
          Distinguisher: [
            'Expert',
            'Triumph',
            'Luxury',
            'Prosper',
            'Admire',
            'Serenity',
            'Dynamic',
            'VIP',
            'Priority',
            'Prestige',
            'Expert',
            'Superior',
            'Simple',
            'King',
            'Dynamic'
          ],
          TradesPlural: [
            'Real Estate',
            'Real Estate Group',
            'Real Estate Agency',
            'Real Estate Team',
            'Real Estate Services',
            'Realtors'
          ],
          TradesSingular: ['Real Estate Agent', 'Estate Agent', 'Realtor'],
          RegionServiced: ['Sydney', 'Alexandria', 'Hunters Hill'],
          Keyword: [
            'Alpha',
            'Real',
            'Homes',
            'Realty',
            'Villas',
            'Housing',
            'Estate',
            'Max',
            'Move'
          ]
        }
      ],
      Templates: [
        ['FirstName', 'The', 'TradesSingular'],
        ['RegionServiced', 'TradesPlural'],
        ['Distinguisher', 'TradesSingular'],
        ['TradesSingular', 'RegionServiced'],
        ['FirstName', 'LastName', 'TradesPlural'],
        ['Mr', 'Keyword', 'TradesPlural'],
        ['Distinguisher', 'TradesPlural'],
        ['Distinguisher', 'Keyword'],
        ['Distinguisher', 'Keyword', 'RegionServiced'],
        ['Distinguisher', 'TradesSingular', 'RegionServiced'],
        ['Keyword', 'Keyword', 'TradesPlural'],
        ['The', 'Distinguisher', 'TradesPlural'],
        ['Keyword', 'Distinguisher'],
        ['Keyword', 'Distinguisher', 'TradesPlural'],
        ['Keyword', 'Distinguisher', 'TradesSingular']
      ],
      Prefix: [
        're',
        'dis',
        'over',
        'un',
        'mis',
        'out',
        'be',
        'co',
        'de',
        'fore',
        'inter',
        'pre',
        'sub',
        'trans',
        'under',
        'anti',
        'auto',
        'bi',
        'co',
        'counter-'
      ],
      Suffix: ['ise', 'ate', 'fy', 'en'],
      names: [],
      order: 3,
      distgram: {},
      related_words: [],
      s_industry: '',
      industry_related_words: []
    };
  },
  created() {
    this.generateNames();
    this.generateDistGrams();
  },
  methods: {
    randomEntry(list) {
      return list[Math.floor(Math.random() * list.length)];
    },
    generateNames() {
      for (let i = 0; i < 500; i++) {
        let founder;
        this.names.push(
          //Randomize Template
          this.randomEntry(this.Templates)
            .map(key => {
              // Checks if key on Template has First/Last Name, if yer return a name of founder
              if (key == 'FirstName' || key == 'LastName') {
                if (!founder) {
                  founder = this.randomEntry(this.Components[0].Founder);
                }
                return founder[key];
              }

              // If key is not name return a random entry on component
              let components = this.Components[0][key];
              if (components) {
                return this.randomEntry(components);
              }

              // Else just return the key
              return key;
            })
            .join(' ')
        );
      }
    },
    generateDistGrams() {
      var words = this.Components[0]['Distinguisher'];
      for (var j = 0; j < words.length; j++) {
        var txt = words[j];

        for (var i = 0; i < txt.length - this.order; i++) {
          var gram = txt.substring(i, i + this.order);
          gram = gram.replace(/^\w/, c => c.toUpperCase());
          if (!this.distgram[gram]) {
            this.distgram[gram] = [];
          }

          this.distgram[gram].push(txt.charAt(i + this.order));
        }
      }
    },
    fetchWordsAPI(keyword) {
      axios
        .get(
          `https://api.datamuse.com/words?rel_trg=${keyword}&ml=${keyword}&max=50`
        )
        .then(response => {
          this.related_words = [];
          response.data.forEach(data => {
            this.related_words.push(data.word);
          });
          this.industryRelatedWordsCombination();
          // this.words = response.data;
        })
        .catch(err => {
          console.log(err);
        });
    },
    industryRelatedWordsCombination() {
      this.related_words.forEach(word => {
        this.industry_related_words.push(
          this.randomEntry(this.IndustryWordCombination)
            .map(key => {
              this.s_industry = this.s_industry.replace(/^\w/, c =>
                c.toLowerCase()
              );
              if (key === 'Industry') return this.s_industry;
              if (key === 'Word') return word;
            })
            .join('')
        );
      });
    }
  }
};
</script>

<style scoped></style>
