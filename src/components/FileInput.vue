<template lang="jade">
  div#fileInput.layout-padding.full-width
    div.row.gutter
      div.col-auto
        button.primary(@click="executarGrasp", v-bind:readonly="loading") {{ loadingText }}
      div.col-auto
        div.stacked-label
          input(v-model="url", placeholder="Insert the server URL")
          label Host URL
      div.col-auto
        div.stacked-label
          input(v-model="iterations", placeholder="Insert the number of iterations")
          label Number of iterations

    div.row.gutter
      div
        FileUpload(@readFile="readingFile" )
        div.stacked-label
          textarea.full-width.no-border(v-model="text", readonly, cols="30", rows="10")
          label Text Json

</template>

<script>
import axios from 'axios'
import dataFromTextFile from '../utils/dataFromTextFile'
import FileUpload from './FileUpload'
export default {
  name: 'FileInput',
  components: {FileUpload},
  data () {
    return {
      url: 'http://localhost:9898',
      data: '',
      text: '',
      grasp: '',
      loading: false,
      loadingText: 'Run',
      iterations: 1000000
    }
  },
  methods: {
    readingFile (text) {
      text = dataFromTextFile.getArrayData(text)
      this.data = dataFromTextFile.getJsonData(text)
      this.data.setup.iterations = this.iterations
      this.text = JSON.stringify(this.data, null, 4)
    },
    executarGrasp () {
      var url = this.url + '/grasp'
      this.loading = true
      this.loadingText = 'Loading'
      axios.post(url, {
        body: this.data
      })
      .then(response => {
        this.loadingText = 'Run'
        this.loading = false
        this.grasp = response
        console.log(response)
        this.$emit('graspReturn', this.grasp)
      })
      .catch(e => {
        this.loading = false
        this.loadingText = 'Run'
        console.log(e)
      })
    }
  }
}
</script>

<style>
</style>
