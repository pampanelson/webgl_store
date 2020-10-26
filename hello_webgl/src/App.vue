<template>
<div id="app">
    <ThreeLabels />
</div>
</template>

<script>
// import HelloThree from './components/HelloThree.vue'
import ThreeLabels from './components/ThreeLabels.vue'
// import HelloShader from './components/shaders/HelloShader.vue'
export default {
    name: 'App',
    components: {
        // HelloShader
        ThreeLabels

    }
}
</script>

<style>
#app {
    width: 100%;
    height: 100%;
}
</style>
