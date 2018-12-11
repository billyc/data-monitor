<template>
  <div class="hello">
    <h1>{{ msg }}</h1>
    <div id="vega"></div>
  </div>
</template>

<script lang="ts">
import { Component, Prop, Vue } from 'vue-property-decorator'
import embed from 'vega-embed'

@Component
export default class HelloWorld extends Vue {
  @Prop() private msg!: string

  public mounted() {
    const spec = '/vega.json'
    embed('#vega', spec, {
      defaultStyle: true,
      renderer: 'svg',
      actions: { export: true, source: false, editor: false },
    }).catch(console.warn)
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
