<template>
  <div>
    <button @click="setRootPath">Open</button>
    <p>
      <input type="checkbox" v-model="includeSubDirs"> Include sub directories
    </p>
    <p>
      Amount: <strong>{{ files.length }}</strong>
    </p>
    <ul>
      <li v-for="file in files">{{ file }}</li>
    </ul>
  </div>
</template>

<script>
  import { isArray, isString } from 'lodash'

  const {resolve} = require('path')
  const {readdir, stat} = require('fs').promises
  const {dialog} = require('electron').remote

  /// ////////////////////////////////////////////////////////////////////////////////////////////////////////////// ///
  /// ////////////////////////////////////////////////////////////////////////////////////////////////////////////// ///
  /// ////////////////////////////////////////////////////////////////////////////////////////////////////////////// ///

  export default {
    name: 'Overview',
    data () {
      return {
        rootPath: null,
        includeSubDirs: false,
        files: []
      }
    },
    methods: {
      setRootPath (rootPath) {
        var path = dialog.showOpenDialog({
          properties: ['openDirectory']
        })

        if (isArray(path) && isString(path[0])) {
          this.rootPath = path[0]
          this.scanDirectory()
        }
      },
      scanDirectory () {
        var includeSubDirs = this.includeSubDirs

        async function getFiles (dir) {
          const subdirs = await readdir(dir)
          const files = await Promise.all(
            subdirs.map(
              async (subdir) => {
                const res = resolve(dir, subdir)
                console.log(includeSubDirs)
                if (includeSubDirs) {
                  return (await stat(res)).isDirectory() ? getFiles(res) : res
                } else {
                  return (await stat(res)).isDirectory() ? [] : res
                }
              }
            )
          )
          return Array.prototype.concat(...files)
        }

        getFiles(this.rootPath).then((files) => {
          this.files = files
        })
      }
    },
    mounted () {
    }
  }
</script>

<style scoped>

</style>