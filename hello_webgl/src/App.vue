<template>
<div id="app">
    <HelloShader />
</div>
</template>

<script>
// import HelloThree from './components/HelloThree.vue'
// import ThreeLabels from './components/ThreeLabels'
import HelloShader from './components/shaders/HelloShader.vue'
export default {
    name: 'App',
    components: {
        HelloShader
    }
}
</script>

<style>
#app {
    width: 100%;
    height: 100%;
}
</style>
