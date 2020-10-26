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
    name: 'ThreeLabels',
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
            textlabels: [],
        }
    },
    methods: {
        init: function () {
            this.container = document.getElementById('container');

            this.camera = new THREE.PerspectiveCamera(70, this.container.clientWidth / this.container.clientHeight, 1, 10000);
            this.camera.position.z = 100;

            this.scene = new THREE.Scene();

            var geometry = new THREE.BoxGeometry(10, 10, 10);
            var material = new THREE.MeshNormalMaterial();

            this.mesh = new THREE.Mesh(geometry, material);
            // this.scene.add(this.mesh);

            this.renderer = new THREE.WebGLRenderer({
                antialias: true
            });

            this.renderer.setClearColor(0x004477);

            this.renderer.setSize(this.container.clientWidth, this.container.clientHeight);
            this.container.appendChild(this.renderer.domElement);

            this.stats = new Stats();
            this.stats.domElement.style.position = 'absolute';
            this.stats.domElement.style.top = '0px';
            this.container.appendChild(this.stats.domElement);

        },

        initControls: function () {
            this.controls = new OrbitControls(this.camera, this.renderer.domElement);

        },

        initLabels: function () {

            // var cube = new THREE.Mesh( geometry, material );
            // var geometry = new THREE.BoxGeometry(10, 10, 10);
            for (var i = 0; i < 20; i++) {

                var geometry = new THREE.BoxGeometry(10, 10, 10);
                var material = new THREE.MeshNormalMaterial();

                var mesh = new THREE.Mesh(geometry, material);

                mesh.position.x = (Math.random() - 0.5) * 100;
                mesh.position.y = (Math.random() - 0.5) * 100;
                mesh.position.z = (Math.random() - 0.5) * 100;
                mesh.updateMatrix();
                mesh.matrixAutoUpdate = false;
                this.scene.add(mesh);

                var text = this._createTextLabel();
                text.setHTML("Label " + i);
                text.setParent(mesh);
                this.textlabels.push(text);
                this.container.appendChild(text.element);
            }

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
            for (var i = 0; i < this.textlabels.length; i++) {
                this.textlabels[i].updatePosition();
                this.textlabels[i].setHTML('Label ' + i + ':' + new Date().getTime());
            }
            this.renderer.render(this.scene, this.camera)
        },

        _createTextLabel: function () {
            var div = document.createElement('div');
            div.className = 'text-label';
            div.style.position = 'absolute';
            div.style.width = 100;
            div.style.height = 100;
            div.innerHTML = "hi there!";
            div.style.top = -1000;
            div.style.left = -1000;
            div.id = new Date().getTime();
            var _this = this;
            div.onclick = function () {
                console.log('div clicked ' + div.id);

            };
            return {
                element: div,
                parent: false,
                position: new THREE.Vector3(0, 0, 0),
                setHTML: function (html) {
                    this.element.innerHTML = html;
                },
                setParent: function (threejsobj) {
                    this.parent = threejsobj;
                },
                updatePosition: function () {
                    if (parent) {
                        this.position.copy(this.parent.position);
                    }

                    var coords2d = this.get2DCoords(this.position, _this.camera);
                    this.element.style.left = coords2d.x + 'px';
                    this.element.style.top = coords2d.y + 'px';
                },
                get2DCoords: function (position, camera) {
                    var vector = position.project(camera);
                    vector.x = (vector.x + 1) / 2 * window.innerWidth;
                    vector.y = -(vector.y - 1) / 2 * window.innerHeight;
                    return vector;
                }
            };
        }
    },
    mounted() {
        this.init();
        this.initControls();
        this.initLabels();
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
