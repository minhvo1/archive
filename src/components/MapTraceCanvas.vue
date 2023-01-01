<script setup>
import p5 from "p5"
import { onMounted, onUnmounted } from 'vue'

const perspective = require('perspective-transform');

let sketch = function(p) {
  let width = 800;
  let height = 800;
  if (window.innerWidth <= 1800) {
    width = 700;
    height = 700;
  }

  let THE_SEED;
  // let border = 200;
  let number_of_particles = 3000;
  let number_of_particle_sets = 8;
  let particle_sets = [];

  let pTransform;

  p.setup = function() {
    p.createCanvas(width, height);
    THE_SEED = p.floor(p.random(9999999));
    p.randomSeed(THE_SEED);

    p.noFill();
    p.background('#e7e7db');
    p.frameRate(60)
    // p.pixelDensity(2048 / width);

    p.stroke(20, 10);
    p.strokeWeight(0.7);
    p.smooth();

    // let srcCorners = [-1, -1, 1, -1, 1, 1, -1, 1];
    // let dstCorners = [-0.8, -0.5, 0.8, -0.5, 1, 0.5, -1, 0.5];

    let srcCorners = [0, 100, width, 0, width, height, 0, height];
    let dstCorners = [100, 300, width - 100, 200, width + 100, height - 100, 0, height - 200];
    pTransform = perspective(srcCorners, dstCorners);

    for (let j = 0; j < number_of_particle_sets; j++) {
      let ps = [];
      for (let i = 0; i < number_of_particles; i++) {
        ps.push(
          new Particle(p.randomGaussian(p.width / 2, 140), p.randomGaussian(p.height / 2, 140), p.random(p.TWO_PI))
        );
      }
      particle_sets.push(ps);
    }

    if (document.getElementById('button-wrapper')) {
      document.getElementById('button-wrapper').remove()
    }

    const buttonWrapper = document.createElement('div');
    buttonWrapper.setAttribute('id', 'button-wrapper')
    document.getElementById('display-wrapper').appendChild(buttonWrapper)

    if (window.innerWidth <= 1800) {
      document.getElementById('button-wrapper').style.width = '700px'
    }

    let resetButton = p.createButton('REGENERATE')
    resetButton.parent(buttonWrapper)
    resetButton.mousePressed(() => {
      location.reload()
    })

    let saveButton = p.createButton('SAVE');
    saveButton.parent(buttonWrapper)
    saveButton.mousePressed(() => {
      p.saveCanvas('mt_' + Math.floor(p.random(100000000, 999999999)))
    })
  };

  p.draw = function() {
    particle_sets.forEach(function(particles, index) {
      particles.forEach(function(particle) {
        particle.update(index);
        particle.display(index);
      });
    });
  };

  class Particle {
    constructor(x, y, phi) {
      this.pos = p.createVector(x, y);
      this.angle = phi;
      this.val = 0;
    }

    update(index) {
      this.pos.x += p.cos(this.angle);
      this.pos.y += p.sin(this.angle);

      let nx = 1.8 * p.map(this.pos.x, 0, p.width, -1, 1);
      let ny = 1.8 * p.map(this.pos.y, 0, p.height, -1, 1);

      pTransform.transformInverse(nx, ny);

      let n = p.createVector(nx, ny);

      this.altitude = p.noise(n.x + 423.2, n.y - 231.1) + 0.05 * p.noise(n.x * 15 + 113.3, n.y * 15 + 221.1);
      let nval = (this.altitude + 0.045 * (index - number_of_particle_sets / 2)) % 1;

      this.angle += 3 * p.map(nval, 0, 1, -1, 1);
      this.val = nval;
    }

    display() {
      if (this.val > 0.479 && this.val < 0.521) {
        //const pnt = pTransform.transform(this.pos.x, this.pos.y);

        let np = pTransform.transform(this.pos.x, this.pos.y + 150 - this.altitude * 350);
        p.point(np[0], np[1]);
      }
    }
  }
};
onMounted(() => {
  let parts = document.URL.split('/')
  if (parts[parts.length - 1] === 'maptrace') {
    document.getElementById('map-trace').classList.add('active');
  }
  new p5(sketch, 'canvas');
})

onUnmounted(() => {
  if (document.getElementsByClassName('active')) {
    document.getElementsByClassName('active').forEach(element => {
      element.classList.remove('active')
    })
  }
})
</script>

<template>
  <div id="canvas"></div>
</template>