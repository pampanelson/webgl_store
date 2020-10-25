<template>
<div id="container"></div>
</template>

<script>
import * as Three from 'three';
import Stats from 'three/examples/jsm/libs/stats.module.js';
import {
    OrbitControls
} from 'three/examples/jsm/controls/OrbitControls.js';

export default {
    name: 'HelloThree',
    props: {},
    components: {},
    data() {
        return {
            camera: null,
            scene: null,
            renderer: null,
            mesh: null,
            stats: null,
            controls: null,
        }
    },
    methods: {
        init: function () {
            var container = document.getElementById('container');

            this.camera = new Three.PerspectiveCamera(70, container.clientWidth / container.clientHeight, 0.01, 10);
            this.camera.position.z = 1;

            this.scene = new Three.Scene();

            var geometry = new Three.BoxGeometry(0.2, 0.2, 0.2);
            var material = new Three.MeshNormalMaterial();

            this.mesh = new Three.Mesh(geometry, material);
            this.scene.add(this.mesh);

            this.renderer = new Three.WebGLRenderer({
                antialias: true
            });
            this.renderer.setSize(container.clientWidth, container.clientHeight);
            container.appendChild(this.renderer.domElement);

            this.stats = new Stats();
            this.stats.domElement.style.position = 'absolute';
            this.stats.domElement.style.top = '0px';
            container.appendChild(this.stats.domElement);

        },

        initControls: function () {
            this.controls = new OrbitControls(this.camera, this.renderer.domElement);

        },

        onWindowResize: function () {
            this.camera.aspect = window.innerWidth / window.innerHeight;
            this.camera.updateProjectionMatrix();
            this.renderer.setSize(window.innerWidth, window.innerHeight);

        },
        animate: function () {
            requestAnimationFrame(this.animate)
            this.mesh.rotation.x += 0.01;
            this.mesh.rotation.y += 0.02;
            this.stats.update();
            this.controls.update();

            this.renderer.render(this.scene, this.camera)
        }
    },
    mounted() {
        this.init()
        this.initControls();
        this.animate()
    },
    created() {
        window.addEventListener('resize', () => {
            this.onWindowResize()
        })
    }
}
</script>

<style>
#container {
    width: 100%;
    height: 800px;
}
</style>
