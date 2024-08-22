<template>
  <div id="app">
    <MonacoEditor v-model="code" language="modelica" theme="vs-dark" />
  </div>
</template>

<script>
import MonacoEditor from './components/MonacoEditor.vue';

export default {
  name: 'App',
  components: {
    MonacoEditor
  },
  data() {
    return {
      code: 'model MyModel\n  // Modelica code here\nend MyModel;'
    };
  }
};
</script>

<style>
html, body, #app {
  margin: 0;
  height: 100%;
  width: 100%;
}

#app {
  display: flex;
  flex-direction: column;
}
</style>