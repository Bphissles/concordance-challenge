<template>
  <header>
    <h1>Concordance for {{name}}</h1>
    <p>Given an arbitrary text document written in English, write a program that will generate a
      concordance, i.e. an alphabetical list of all word occurrences, labeled with word
      frequencies.
    </p>
    <p>
      <strong>Bonus:</strong> label each word with the sentence numbers in which each occurrence appeared.
    </p>
  </header>

<section id="introduction">
  <button @click="showExample = !showExample">{{showExample ? 'Hide Example' : 'Show Example'}}</button>
  <con-example v-if="showExample"/>
  <br>
  <br>
</section>

<section id="interaction">
  <textarea name="copy-body" id="copy-body" rows="4" cols="70" v-model="copyBody"></textarea>
  <br>
  <br>
  <button @click="concordance(copyBody)">Run Test</button>
  <ol type=a>
    <li v-for="(word,index) in cleanEntry" :key="index">
      <div class="list-item">
        <span class="word-instance">{{word.word}}</span> 
        <span class="occurrence">&#123;{{word.occurrences}}:{{word.sentence}}&#125;</span>
      </div>
    </li>
  </ol>
</section>
  
</template>

<script>
import ConExample from './ConExample.vue'
export default {
  name: 'Concordance',
  props: ['name'],
  components: {
    ConExample
  },
  data() {
    return {
      showExample: false,
      copyBody: 'Hello Everybody',
      counts: {},
      sentenceOccurrence: [],
      cleanEntry: []
    }
  },
  methods: {
    concordance(userCopy) {
      this.counts = {}
      const entry = []
      let sentenceArrayInit = []
      let sentenceArray = []
      let wordArray = []

      //setup arrays
      sentenceArrayInit = userCopy.split('\n')
      sentenceArrayInit = sentenceArrayInit.filter(element => element.length > 0)

      sentenceArray = sentenceArrayInit.map(element => {
        return element.toLowerCase()
      })
      sentenceArray.forEach(element => {
        wordArray = wordArray.concat(this.stringClean(element))
      })

      // add word occurrences 
      wordArray = wordArray.sort()
      wordArray.forEach(element => {
        this.sentenceOccurrence = []
        this.counts[element] = (this.counts[element] || 0) + 1 //occurrence count

        // check sentences
        sentenceArray.forEach((innerElement, index) => {
          if (innerElement.includes(element)) {
            let wordList = this.stringClean(innerElement)

            let result = wordList.filter(word => word === element)
            result = result.map(() => index + 1)

            if (result.length > 0) {
              this.sentenceOccurrence.push(result)
            }
          }
        })

        entry.push({
          word: element,
          occurrences: this.counts[element],
          sentence: this.sentenceOccurrence
        })
      })

      //de-dupe entry
      for (let ii = 0; ii < entry.length - 1; ii++) {
        if (entry[ii].word === entry[ii + 1].word) {
          delete entry[ii]
        }
      }
      // filter empty entries
      this.cleanEntry = entry.filter(n => n)

      //combine arrays set to string
      this.cleanEntry.forEach(element => {
        element.sentence = element.sentence.flat().toString()
      })
    },

    stringClean(element) {
      return element.replace(/[.,/#!$%^&*;:{}=\-_`~()]/g, "").replaceAll("'", '').toLowerCase().split(' ')
    }
  }
}
</script>

<style>
  .list-item {
    display: flex;
    justify-content: space-between;
    width: 350px;
    margin-left: 1rem;
  }
</style>