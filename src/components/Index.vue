<template>
  <div>
    <h1>MY DAD IS A POOP</h1>
    <div
      style="padding:5px;"
      id="output"
      v-for="(name, index) in names"
      :key="index"
    >
      {{ name }}
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
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
      names: [],
      order: 3,
      distgram: {}
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
    }
  }
};
</script>

<style scoped></style>
