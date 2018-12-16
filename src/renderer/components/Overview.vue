<template>
  <div>
    <button @click="setRootPath">Open</button>
    <ul>
      <li v-for="file in files">{{ file }}</li>
    </ul>
  </div>
</template>

<script>
  const fs = require('fs')
  const {dialog} = require('electron').remote

  /// ////////////////////////////////////////////////////////////////////////////////////////////////////////////// ///
  /// ////////////////////////////////////////////////////////////////////////////////////////////////////////////// ///
  /// ////////////////////////////////////////////////////////////////////////////////////////////////////////////// ///

  export default {
    name: 'Overview',
    data () {
      return {
        rootPath: null,
        files: []
      }
    },
    methods: {
      setRootPath (rootPath) {
        var path = dialog.showOpenDialog({
          properties: ['openDirectory']
        })

        this.rootPath = path[0]
        this.scanDirectory()
      },
      scanDirectory () {
        this.files = []
        fs.readdir(this.rootPath, (err, dir) => {
          if (err) {

          } else {
            dir.forEach((path, idx) => {
              this.files.push(path)
            })
          }
        })
      }
    },
    mounted () {
    }
  }
</script>

<style scoped>

</style>