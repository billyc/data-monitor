<template>
  <div class="hello">
    <h1>{{ msg }}</h1>
    <p>
      <input size="8" v-model="entry" placeholder="kg">
      <input size="12" v-model="entryDate">
      <button @click="addEntry">Add Entry</button>
      <button @click="removeLastEntry">Delete Last</button>
    </p>
    <hr>
    <div id="vega"></div>
  </div>
</template>

<script lang="ts">
import { Component, Prop, Vue } from 'vue-property-decorator'
import embed from 'vega-embed'

import * as firebase from 'firebase/app'
import 'firebase/auth'
import ui from 'firebaseui'
import { isNumber } from 'util'

const EXP_FACTOR = 0.12

@Component
export default class HelloWorld extends Vue {
  @Prop() private msg!: string

  private entry: any = null
  private entryDate: string = ''

  private spec: any

  private firebaseConfig = {
    apiKey: 'AIzaSyAyyaN-E09dQInw1eNv5ObRHtr9S-5UFRY',
    authDomain: 'weight-e08c9.firebaseapp.com',
    databaseURL: 'https://weight-e08c9.firebaseio.com',
    projectId: 'weight-e08c9',
    storageBucket: '',
    messagingSenderId: '956201356607',
    appId: '1:956201356607:web:f42c8d0523a3ace1',
  }

  public created() {
    firebase.initializeApp(this.firebaseConfig)

    /* ui.start('#firebaseui-auth-container', {
      signInOptions: [firebase.auth.EmailAuthProvider.PROVIDER_ID, firebase.auth.GoogleAuthProvider.PROVIDER_ID],
      // Other config options...
    }) */
  }

  public async mounted() {
    const today = new Date()
    const todaysDate = `${today.getFullYear()}-${today.getMonth() + 1}-${today.getDate()}`
    this.entryDate = todaysDate

    let myData: any = localStorage.getItem('vega')
    console.log({ myData })

    if (myData) {
      this.spec = JSON.parse(myData)
    } else {
      myData = '/data-vega.json'
      const response = await fetch('/data-vega.json')
      this.spec = await response.json()
    }

    embed('#vega', this.spec, {
      defaultStyle: true,
      renderer: 'svg',
      actions: { export: true, source: false, editor: false },
    }).catch(console.warn)
  }

  public removeLastEntry() {
    const values = this.spec.data.values
    values.pop()

    embed('#vega', this.spec, {
      defaultStyle: true,
      renderer: 'svg',
      actions: { export: true, source: false, editor: false },
    }).catch(console.warn)

    localStorage.setItem('vega', JSON.stringify(this.spec))
  }

  public addEntry() {
    console.log(this.entry)
    const weight = parseFloat(this.entry) // will fail on non number

    const values = this.spec.data.values

    const last = values.length ? values[values.length - 1] : null
    const trend = last ? last.trend + EXP_FACTOR * (weight - last.trend) : weight

    const row = {
      date: this.entryDate,
      weight,
      trend,
    }
    console.log({ row })
    values.push(row)

    embed('#vega', this.spec, {
      defaultStyle: true,
      renderer: 'svg',
      actions: { export: true, source: false, editor: false },
    }).catch(console.warn)

    localStorage.setItem('vega', JSON.stringify(this.spec))
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
</style>

