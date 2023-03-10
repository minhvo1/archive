<script setup>
import p5 from "p5"
import { onMounted, onUnmounted } from 'vue'

const sketch = function(p) {
  let width = 800;
  let height = 800;
  if (window.innerWidth <= 1800) {
    width = 700;
    height = 700;
  }

  let initial_size = p.random(3, 7)
  let initial_deviation = p.random(70, 1000);
  let deviation = p.random(50, 150);

  let number_of_interpolations = p.random(4, 9)
  let number_of_layers = p.random(4, 10);
  let shapes_per_layer = p.random(30, 100);

  let points;
  
  let current_palette = [];
  let random_palette = [];

  let COLOR_SEED;
  let SHAPE_SEED;

  p.setup = function() {
    p.createCanvas(width, height);
    p.pixelDensity(2048 / width);
    p.colorMode(p.HSB);
    p.blendMode(p.MULTIPLY);
    p.noStroke();
    p.noLoop();

    if (document.getElementById('button-wrapper')) {
      document.getElementById('button-wrapper').remove()
    }

    const buttonWrapper = document.createElement('div')
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
      p.saveCanvas('wa_' + Math.floor(p.random(100000000, 999999999)))
    })
  };

  p.draw = function() {
    set_seeds();
    p.randomSeed(COLOR_SEED);
    set_colors();
    p.randomSeed(SHAPE_SEED);
    display();
  };

  function set_seeds() {
    COLOR_SEED = p.floor(Math.random() * 100000000);
    SHAPE_SEED = p.floor(Math.random() * 100000000);
  }

  function set_colors() {
    random_palette = [];
    for (let i = 0; i < number_of_layers; i++) {
      random_palette.push(p.random(360));
    }
  }

  function display() {
    p.clear();
    p.background('#fff');
    for (let h = 0; h < number_of_layers; h++) {
      init(h * 250 - 100);
      let hue = random_palette[h];
      current_palette[h] = hue;
      p.fill(hue, 100, 95, 0.012);
      for (let i = 0; i < shapes_per_layer; i++) {
        let shape = get_letiant();
        display_shape(shape);
      }
    }
  }

  function init(ypos) {
    points = [];
    for (let i = 0; i < initial_size; i++) {
      let vec = p.createVector(i / (initial_size - 1) * p.width, ypos, p.random(-1, 1));
      move_nearby(vec, initial_deviation);
      points.push(vec);
    }
    for (let b = 0; b < number_of_interpolations; b++) {
      interpolate(points, initial_deviation);
    }
  }

  function get_letiant() {
    let c = deep_copy(points);
    for (let b = 0; b < 8; b++) {
      c.forEach(function(pnt) {
        move_nearby(pnt, deviation);
      }, this);
    }
    return c;
  }

  function display_shape(shape) {
    p.beginShape();
    p.vertex(0, shape[0].y);

    shape.forEach(function(pnt) {
      p.vertex(pnt.x, pnt.y);
    });

    p.vertex(p.width, shape[shape.length - 1].y);
    p.vertex(p.width, p.height);
    p.vertex(0, p.height);
    p.endShape(p.CLOSE);
  }

  function interpolate(points, sd) {
    for (let i = points.length - 1; i > 0; i--) {
      points.splice(i, 0, generate_midpoint(points[i - 1], points[i], sd));
    }
  }

  function generate_midpoint(p1, p2, sd) {
    let p3 = p.createVector((p1.x + p2.x) / 2, (p1.y + p2.y) / 2, (p1.z + p2.z) / 2 * 0.45 * p.random(0.1, 3));
    move_nearby(p3, sd);
    return p3;
  }

  let move_nearby = function(pnt, sd) {
    pnt.x = p.randomGaussian(pnt.x, pnt.z * sd);
    pnt.y = p.randomGaussian(pnt.y, pnt.z * sd);
  };

  let deep_copy = function(arr) {
    let narr = [];
    for (let i = 0; i < arr.length; i++) {
      narr.push(arr[i].copy());
    }
    return narr;
  };

};

onMounted(() => {
  let parts = document.URL.split('/')
  if (parts[parts.length - 1] === 'watercolor') {
    document.getElementById('watercolor').classList.add('active');
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