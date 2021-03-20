<template>
  
  
  <el-tabs v-model="activeName" type="border-card">
    <el-tab-pane label="Technique" name="first"><Technique/></el-tab-pane>
    <el-tab-pane label="Question" name="second"><Question/></el-tab-pane>
  </el-tabs>
</template>

<script>
import Technique from './components/Technique.vue'
import Question from './components/Question.vue'

export default {
  name: 'App',
  components: {
    Technique, Question
  },
  data() {
      return {
        activeName: 'first'
      };
    },
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
