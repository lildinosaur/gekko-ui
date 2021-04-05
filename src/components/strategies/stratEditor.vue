<template>
  <div>
    <div class="row">
      <q-btn color="primary" >Save</q-btn>  
      <q-btn color="primary" >Cancel</q-btn>  
    </div>
    <div class="row">
      <div class="col">
        <div id="container" style="height:600px;border:1px solid #ccc"></div>
      </div>
    </div>
  </div>
</template>

<script>
  import _ from 'lodash'
  import * as monaco from 'monaco-editor';
  import StrategyService from '../mixins/StrategyService'

  export default {
    mixins: [StrategyService],
    methods: {
      emitConfig: function () {
        this.parseParams();
        this.$emit('stratConfig', this.config);
      },
      parseParams: function () {
        try {
          this.stratParams = toml.parse(this.rawStratParams);
          this.rawStratParamsError = false;
        } catch (e) {
          this.rawStratParamsError = e;
          this.stratParams = {};
        }
      }
    },
    async mounted() {
      self.MonacoEnvironment = {
        getWorkerUrl: function(moduleId, label) {
          return `data:text/javascript;charset=utf-8,${encodeURIComponent(`
              self.MonacoEnvironment = {
                baseUrl: 'https://cdnjs.cloudflare.com/ajax/libs/monaco-editor/0.23.0/min'
              };
              importScripts('https://cdnjs.cloudflare.com/ajax/libs/monaco-editor/0.23.0/min/vs/base/worker/workerMain.js');`)}`
        }
      };
      const editor = monaco.editor.create(document.getElementById('container'), {
        value: 'console.log("Hello, world")',
        language: 'javascript',
        theme:"vs-dark"
      });

      editor.onDidChangeModelContent(event => {
        const value = editor.getValue();
        if (this.value !== value) {
          //this.$emit('change', value, event)
          console.log(value);
        }
      });
      /*
      const diffEditor = monaco.editor.createDiffEditor(document.getElementById('container'));

      diffEditor.setModel({
        original: monaco.editor.createModel("console.log('pouet');", 'javascript'),
        modified: monaco.editor.createModel("console.log('prout');", 'javascript')
      });

      const editor = diffEditor.getModifiedEditor();
      editor.onDidChangeModelContent(event => {
        const value = editor.getValue()
        if (this.value !== value) {
          console.log(value);
        }
      })*/
    }
  };
</script>
