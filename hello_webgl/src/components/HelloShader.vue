<template>
<div id="container"></div>
</template>

<script>
import * as THREE from 'three';
import Stats from 'three/examples/jsm/libs/stats.module.js';
import {
    OrbitControls
} from 'three/examples/jsm/controls/OrbitControls.js';

export default {
    name: 'HelloShader',
    props: {},
    components: {},
    data() {
        return {
            container: null,
            camera: null,
            scene: null,
            renderer: null,
            mesh: null,
            stats: null,
            controls: null,
            vertShader: null,
            fragShader: null,
        }
    },
    methods: {
        init: function () {
            this.container = document.getElementById('container');

            this.camera = new THREE.PerspectiveCamera(70, this.container.clientWidth / this.container.clientHeight, 1, 10000);
            this.camera.position.z = 10;

            this.scene = new THREE.Scene();

            let uniforms = {
                colorB: {
                    type: 'vec3',
                    value: new THREE.Color(0xACB6E5)
                },
                colorA: {
                    type: 'vec3',
                    value: new THREE.Color(0x74ebd5)
                }
            }

            let geometry = new THREE.BoxGeometry(1, 1, 1);
            let material = new THREE.ShaderMaterial({
                uniforms: uniforms,
                fragmentShader: this.fragShader,
                vertexShader: this.vertShader,
            })

            this.mesh = new THREE.Mesh(geometry, material)
            this.mesh.position.x = 2;
            this.scene.add(this.mesh);

            this.renderer = new THREE.WebGLRenderer({
                antialias: true
            });
            this.renderer.setSize(this.container.clientWidth, this.container.clientHeight);
            this.container.appendChild(this.renderer.domElement);

            this.stats = new Stats();
            this.stats.domElement.style.position = 'absolute';
            this.stats.domElement.style.top = '0px';
            this.container.appendChild(this.stats.domElement);

        },

        initShaders: function () {
            this.vertShader = `
                varying vec3 vUv; 

                void main() {
                    vUv = position; 

                    vec4 modelViewPosition = modelViewMatrix * vec4(position, 1.0);
                    gl_Position = projectionMatrix * modelViewPosition; 
                }
            `;
            this.fragShader = `
                uniform vec3 colorA; 
                uniform vec3 colorB; 
                varying vec3 vUv;

                void main() {
                    gl_FragColor = vec4(mix(colorA, colorB, vUv.z), 1.0);
                }
            `;

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
        this.initShaders();
        this.init();
        this.initControls();
        this.animate();
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
