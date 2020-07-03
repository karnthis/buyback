<template>
  <div class="buyback">
  <v-row>
    <v-col cols="12" md="6">
      <h2>
        Paste Buyback Items Here
      </h2>
      <v-textarea
        solo
        name="buyback-input"
        label="Paste..."
        v-model="buybackSubmission"
      ></v-textarea>
      <v-btn
        class="white--text"
        color="primary"
        @click='runSubmission'
      >
        Submit for Appraisal
      </v-btn>
<!--    </v-col>-->
<!--    <v-col>-->
      <p></p>
      <h3>
        Buyback Price:
      </h3>
      <span v-if="buybackValue != 0">{{ buybackValue.toLocaleString() }} isk</span>
<!--    </v-col>-->
<!--    <v-col>-->
      <h3 v-if="rejectedList.length">
        Rejected Items:
      </h3>
      <v-list >
        <v-list-item v-for="row in rejectedList.filter(item => !!item)" :key="row.id">
          {{ row }}
        </v-list-item>
      </v-list>
    </v-col>
    <v-spacer></v-spacer>
<!--    <v-col cols="12" md="1">-->
<!--    </v-col>-->
    <v-col cols="12" md="4">
      <h2>
        Instructions
      </h2>
        <ol>
      <li>
        Open your inventory and switch to Details or List view
      </li>
      <li>
        Select the ores and minerals you wish to sell (no moon ore or goo accepted at this time) and copy them with Ctrl+C
      </li>
      <li>
        Paste the list of ores into the field to the left of these directions
      </li>
      <li>
        Press "Submit for Appraisal" and wait for your submission to process
      </li>
      <li>
        Once you have the value of your buyback contract, create a new contract to "API Testing Corp" with the given price and items
      </li>
    </ol>
      <p></p>
      <h3>
        **Please Note** Buyback contracts are only accepted from:
      </h3>
      <ul>
        <li>Hibi</li>
        <li>Gemodi</li>
        <li>Chamume</li>
        <li>Mamet</li>
      </ul>

    </v-col>
    <v-spacer></v-spacer>
  </v-row>
  </div>
</template>

<script>
export default {
  name: 'BuybackCalculator',
  components: {},
  data: () => ({
    buybackSubmission: '',
    refineRate: 0.81,
    buybackRate: 0.9,
    buybackValue: 0,
    whitelist: [
      'tritanium',
      'pyerite',
      'mexallon',
      'isogen',
      'nocxium',
      'zydrine',
      'megacyte',
      'morphite',
      'arkonor',
      'crimson arkonor',
      'prime arkonor',
      'flawless arkonor',
      'compressed arkonor',
      'compressed crimson arkonor',
      'compressed prime arkonor',
      'compressed flawless arkonor',
      'bistot',
      'triclinic bistot',
      'monoclinic bistot',
      'cubic bistot',
      'compressed bistot',
      'compressed triclinic bistot',
      'compressed monoclinic bistot',
      'compressed cubic bistot',
      'crokite',
      'sharp crokite',
      'crystalline crokite',
      'pellucid crokite',
      'compressed crokite',
      'compressed sharp crokite',
      'compressed crystalline crokite',
      'compressed pellucid crokite',
      'dark ochre',
      'onyx ochre',
      'obsidian ochre',
      'jet ochre',
      'compressed dark ochre',
      'compressed onyx ochre',
      'compressed obsidian ochre',
      'compressed jet ochre',
      'gneiss',
      'iridescent gneiss',
      'prismatic gneiss',
      'brilliant gneiss',
      'compressed gneiss',
      'compressed iridescent gneiss',
      'compressed prismatic gneiss',
      'compressed brilliant gneiss',
      'hedbergite',
      'vitric hedbergite',
      'glazed hedbergite',
      'lustrous hedbergite',
      'compressed hedbergite',
      'compressed vitric hedbergite',
      'compressed glazed hedbergite',
      'compressed lustrous hedbergite',
      'hemorphite',
      'vivid hemorphite',
      'radiant hemorphite',
      'scintillating hemorphite',
      'compressed hemorphite',
      'compressed vivid hemorphite',
      'compressed radiant hemorphite',
      'compressed scintillating hemorphite',
      'jaspet',
      'pure jaspet',
      'pristine jaspet',
      'immaculate jaspet',
      'compressed jaspet',
      'compressed pure jaspet',
      'compressed pristine jaspet',
      'compressed immaculate jaspet',
      'kernite',
      'luminous kernite',
      'fiery kernite',
      'resplendant kernite',
      'compressed kernite',
      'compressed luminous kernite',
      'compressed fiery kernite',
      'compressed resplendant kernite',
      'mercoxit',
      'magma mercoxit',
      'vitreous mercoxit',
      'compressed mercoxit',
      'compressed magma mercoxit',
      'compressed vitreous mercoxit',
      'omber',
      'silvery omber',
      'golden omber',
      'platinoid omber',
      'compressed omber',
      'compressed silvery omber',
      'compressed golden omber',
      'compressed platinoid omber',
      'plagioclase',
      'azure plagioclase',
      'rich plagioclase',
      'sparkling plagioclase',
      'compressed plagioclase',
      'compressed azure plagioclase',
      'compressed rich plagioclase',
      'compressed sparkling plagioclase',
      'pyroxeres',
      'solid pyroxeres',
      'viscous pyroxeres',
      'opulent pyroxeres',
      'compressed pyroxeres',
      'compressed solid pyroxeres',
      'compressed viscous pyroxeres',
      'compressed opulent pyroxeres',
      'scordite',
      'condensed scordite',
      'massive scordite',
      'glossy scordite',
      'compressed scordite',
      'compressed condensed scordite',
      'compressed massive scordite',
      'compressed glossy scordite',
      'spodumain',
      'bright spodumain',
      'gleaming spodumain',
      'dazzling spodumain',
      'compressed spodumain',
      'compressed bright spodumain',
      'compressed gleaming spodumain',
      'compressed dazzling spodumain',
      'veldspar',
      'concentrated veldspar',
      'dense veldspar',
      'stable veldspar',
      'compressed veldspar',
      'compressed concentrated veldspar',
      'compressed dense veldspar',
      'compressed stable veldspar'
    ],
    rejectedList: [],
    condenser: {}

  }),
  methods: {
    async runSubmission () {
      this.rejectedList = []
      this.condenser = {}
      this.buybackValue = 0

      const finalRawMaterials = {
        tritanium: 0,
        pyerite: 0,
        mexallon: 0,
        isogen: 0,
        nocxium: 0,
        zydrine: 0,
        megacyte: 0,
        morphite: 0
      }
      this.parsePaste(this.buybackSubmission, finalRawMaterials)
      console.dir(finalRawMaterials)
      const fetchedValues = await this.doPriceFetch(this.buildFetchedIds())
      const usableValues = fetchedValues.reduce((acc, curr) => {
        acc[curr.itemID] = curr.highBuy
        return acc
      }, {})
      this.buybackValue += finalRawMaterials.tritanium * usableValues[34]
      this.buybackValue += finalRawMaterials.pyerite * usableValues[35]
      this.buybackValue += finalRawMaterials.mexallon * usableValues[36]
      this.buybackValue += finalRawMaterials.isogen * usableValues[37]
      this.buybackValue += finalRawMaterials.nocxium * usableValues[38]
      this.buybackValue += finalRawMaterials.zydrine * usableValues[39]
      this.buybackValue += finalRawMaterials.megacyte * usableValues[40]
      this.buybackValue += finalRawMaterials.morphite * usableValues[11399]

      console.dir(fetchedValues)
    },
    parseRow (row) {
      const fragments = row.split('\t')
      const lowerFragment = fragments[0].toLowerCase()
      if (this.whitelist.includes(lowerFragment)) {
        if (this.condenser[lowerFragment]) {
          // console.log(fragments[1])
          this.condenser[lowerFragment] += Number(fragments[1].replace(/\.|,/g, ''))
        } else {
          // console.log(fragments[1])
          this.condenser[lowerFragment] = Number(fragments[1].replace(/\.|,/g, ''))
        }
      } else {
        this.rejectedList.push(fragments[0])
      }
    },
    parsePaste (body, finalRawMaterials) {
      const rows = body.split('\n')
      for (const row of rows) {
        this.parseRow(row)
      }
      // console.dir(this.condenser)
      // console.dir(this.rejectedList)
      for (const key in this.condenser) {
        this.calculateAllMinerals(key, this.condenser[key], finalRawMaterials)
      }
    },
    buildFetchedIds (data) {
      // All minerals
      return [
        37,
        40,
        36,
        11399,
        38,
        35,
        34,
        39
      ].join(',')
    },
    doPriceFetch (toFetch) {
      return fetch(`https://api2.squirrellogic.com/esi?key=42&type_id=${toFetch}`, {
        mode: 'cors'
      })
        .then(resp => {
          return resp.json()
        })
    },
    calculateOneOre (ore, count, modifier, finalRawMaterials) {
      for (const key in ore) {
        finalRawMaterials[key] += Math.floor(ore[key] * count * modifier * this.refineRate * this.buybackRate)
      }
    },
    calculateAllMinerals (ore, count, finalRawMaterials) {
      const oreParts = ore.split(' ')
      if (oreParts[0] === 'compressed') {
        oreParts.shift()
      } else {
        count = Math.floor(count / 100)
      }
      let modifier = 0
      let oreMinerals = {}
      const trueName = oreParts.join(' ')
      switch (oreParts[oreParts.length - 1]) {
        case 'tritanium':
          finalRawMaterials.tritanium += Math.floor(count * this.buybackRate)
          break
        case 'pyerite':
          finalRawMaterials.pyerite += Math.floor(count * this.buybackRate)
          break
        case 'mexallon':
          finalRawMaterials.mexallon += Math.floor(count * this.buybackRate)
          break
        case 'isogen':
          finalRawMaterials.isogen += Math.floor(count * this.buybackRate)
          break
        case 'nocxium':
          finalRawMaterials.nocxium += Math.floor(count * this.buybackRate)
          break
        case 'zydrine':
          finalRawMaterials.zydrine += Math.floor(count * this.buybackRate)
          break
        case 'megacyte':
          finalRawMaterials.megacyte += Math.floor(count * this.buybackRate)
          break
        case 'morphite':
          finalRawMaterials.morphite += Math.floor(count * this.buybackRate)
          break
        case 'arkonor':
          oreMinerals = {
            tritanium: 22000,
            mexallon: 2500,
            megacyte: 320
          }
          if (trueName === 'crimson arkonor') {
            modifier = 1.05
          } else if (trueName === 'prime arkonor') {
            modifier = 1.1
          } else {
            modifier = 1
          }
          this.calculateOneOre(oreMinerals, count, modifier, finalRawMaterials)
          break
        case 'bistot':
          oreMinerals = {
            pyerite: 12000,
            zydrine: 450,
            megacyte: 100
          }
          if (trueName === 'triclinic bistot') {
            modifier = 1.05
          } else if (trueName === 'monoclinic bistot') {
            modifier = 1.1
          } else {
            modifier = 1
          }
          this.calculateOneOre(oreMinerals, count, modifier, finalRawMaterials)
          break
        case 'crokite':
          oreMinerals = {
            tritanium: 21000,
            nocxium: 760,
            zydrine: 135
          }
          if (trueName === 'sharp crokite') {
            modifier = 1.05
          } else if (trueName === 'crystalline crokite') {
            modifier = 1.1
          } else {
            modifier = 1
          }
          this.calculateOneOre(oreMinerals, count, modifier, finalRawMaterials)
          break
        case 'ochre':
          oreMinerals = {
            tritanium: 10000,
            isogen: 1600,
            nocxium: 120
          }
          if (trueName === 'onyx ochre') {
            modifier = 1.05
          } else if (trueName === 'obsidian ochre') {
            modifier = 1.1
          } else {
            modifier = 1
          }
          this.calculateOneOre(oreMinerals, count, modifier, finalRawMaterials)
          break
        case 'gneiss':
          oreMinerals = {
            pyerite: 2200,
            mexallon: 2400,
            isogen: 300
          }
          if (trueName === 'iridescent gneiss') {
            modifier = 1.05
          } else if (trueName === 'prismatic gneiss') {
            modifier = 1.1
          } else {
            modifier = 1
          }
          this.calculateOneOre(oreMinerals, count, modifier, finalRawMaterials)
          break
        case 'hedbergite':
          oreMinerals = {
            pyerite: 1000,
            isogen: 200,
            nocxium: 100,
            zydrine: 19
          }
          if (trueName === 'vitric hedbergite') {
            modifier = 1.05
          } else if (trueName === 'glazed hedbergite') {
            modifier = 1.1
          } else {
            modifier = 1
          }
          this.calculateOneOre(oreMinerals, count, modifier, finalRawMaterials)
          break
        case 'hemorphite':
          oreMinerals = {
            tritanium: 2200,
            isogen: 100,
            nocxium: 120,
            zydrine: 15
          }
          if (trueName === 'vivid hemorphite') {
            modifier = 1.05
          } else if (trueName === 'radiant hemorphite') {
            modifier = 1.1
          } else {
            modifier = 1
          }
          this.calculateOneOre(oreMinerals, count, modifier, finalRawMaterials)
          break
        case 'jaspet':
          oreMinerals = {
            mexallon: 350,
            nocxium: 75,
            zydrine: 8
          }
          if (trueName === 'pure jaspet') {
            modifier = 1.05
          } else if (trueName === 'pristine jaspet') {
            modifier = 1.1
          } else {
            modifier = 1
          }
          this.calculateOneOre(oreMinerals, count, modifier, finalRawMaterials)
          break
        case 'kernite':
          oreMinerals = {
            tritanium: 134,
            mexallon: 267,
            isogen: 134
          }
          if (trueName === 'luminous kernite') {
            modifier = 1.05
          } else if (trueName === 'fiery kernite') {
            modifier = 1.1
          } else {
            modifier = 1
          }
          this.calculateOneOre(oreMinerals, count, modifier, finalRawMaterials)
          break
        case 'mercoxit':
          oreMinerals = {
            morphite: 300
          }
          if (trueName === 'magma mercoxit') {
            modifier = 1.05
          } else if (trueName === 'vitreous mercoxit') {
            modifier = 1.1
          } else {
            modifier = 1
          }
          this.calculateOneOre(oreMinerals, count, modifier, finalRawMaterials)
          break
        case 'omber':
          oreMinerals = {
            tritanium: 800,
            pyerite: 100,
            isogen: 85
          }
          if (trueName === 'silvery omber') {
            modifier = 1.05
          } else if (trueName === 'golden omber') {
            modifier = 1.1
          } else {
            modifier = 1
          }
          this.calculateOneOre(oreMinerals, count, modifier, finalRawMaterials)
          break
        case 'plagioclase':
          oreMinerals = {
            tritanium: 107,
            pyerite: 213,
            mexallon: 107
          }
          if (trueName === 'azure plagioclase') {
            modifier = 1.05
          } else if (trueName === 'rich plagioclase') {
            modifier = 1.1
          } else {
            modifier = 1
          }
          this.calculateOneOre(oreMinerals, count, modifier, finalRawMaterials)
          break
        case 'pyroxeres':
          oreMinerals = {
            tritanium: 351,
            pyerite: 25,
            mexallon: 50,
            nocxium: 5
          }
          if (trueName === 'solid pyroxeres') {
            modifier = 1.05
          } else if (trueName === 'viscous pyroxeres') {
            modifier = 1.1
          } else {
            modifier = 1
          }
          this.calculateOneOre(oreMinerals, count, modifier, finalRawMaterials)
          break
        case 'scordite':
          oreMinerals = {
            tritanium: 346,
            pyerite: 173
          }
          if (trueName === 'condensed scordite') {
            modifier = 1.05
          } else if (trueName === 'massive scordite') {
            modifier = 1.1
          } else {
            modifier = 1
          }
          this.calculateOneOre(oreMinerals, count, modifier, finalRawMaterials)
          break
        case 'spodumain':
          oreMinerals = {
            tritanium: 56000,
            pyerite: 12050,
            mexallon: 2100,
            isogen: 450
          }
          if (trueName === 'bright spodumain') {
            modifier = 1.05
          } else if (trueName === 'gleaming spodumain') {
            modifier = 1.1
          } else {
            modifier = 1
          }
          this.calculateOneOre(oreMinerals, count, modifier, finalRawMaterials)
          break
        case 'veldspar':
          oreMinerals = {
            tritanium: 410
          }
          if (trueName === 'concentrated veldspar') {
            modifier = 1.05
          } else if (trueName === 'dense veldspar') {
            modifier = 1.1
          } else {
            modifier = 1
          }
          this.calculateOneOre(oreMinerals, count, modifier, finalRawMaterials)
          break
        default:
      }
      return finalRawMaterials
    }
  }
}
</script>
