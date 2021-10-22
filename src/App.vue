<template>
  <Header />
  <ResInputs />
  <ProdDataInput />
</template>

<script>
import Header from './components/Header'
import ResInputs from './components/ResInputs'
import ProdDataInput from './components/ProdDataInput'


export default {
  name: 'App',
  components: {
    Header,
    ResInputs,
    ProdDataInput
  }
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
