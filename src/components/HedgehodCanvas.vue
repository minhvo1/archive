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
  let offset = -70;
  let circular_shape = false;

  let flow_cell_size = p.random(1.8, 3);
  let number_of_layers = 1;

  let vertical_partitions = 1;
  let horizontal_partitions = 1;

  let vertical_shift = 200;
  let horizontal_shift = 400;

  let noise_size = p.random(0.005, 0.04);
  let noise_radius = 0.01;

  let flow_width = (width + offset * 2) / flow_cell_size;
  let flow_height = (height + offset * 2) / flow_cell_size;

  let flow_grid = [];
  let noise_offset_x, noise_offset_y;

  p.setup = function() {
    p.createCanvas(width, height);
    p.smooth();
    p.noLoop();

    p.stroke(255, 20);
    p.strokeWeight(1);
    p.pixelDensity(2048 / width);
  
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
      p.saveCanvas('hh_' + Math.floor(p.random(100000000, 999999999)))
    })

  };
  
  p.draw = function() {
    p.background('#000');
    p.translate(-offset, -offset);

    for (var i = 0; i < number_of_layers; i++) {
      noise_offset_x = p.random(10);
      noise_offset_y = p.random(10);
      init_flow();
      display_flow(i);
    }
  };

  function init_flow() {
    flow_grid = [];
    for (let i = 0; i < flow_height; i++) {
      let row = [];
      for (let j = 0; j < flow_width; j++) {
        row.push(
          calculate_flow(
            (j + vertical_shift * p.floor((vertical_partitions * j) / flow_height)) *
              noise_size,
            (i + horizontal_shift * p.floor((horizontal_partitions * i) / flow_width)) *
              noise_size,
            noise_radius
          )
        );
      }
      flow_grid.push(row);
    }
  }

  function calculate_flow(x, y, r) {
    let mean_arrow = p.createVector(0, 0);
    let radial_samples = 15;
    for (var i = 0; i < radial_samples; i++) {
      let angle = p.random(p.PI);
      let pos1 = p.createVector(x + p.cos(angle) * r, y + p.sin(angle) * r);
      let pos2 = p.createVector(x + p.cos(angle + p.PI) * r, y + p.sin(angle + p.PI) * r);

      let val1 = p.noise(noise_offset_x + pos1.x, noise_offset_y + pos1.y);
      let val2 = p.noise(noise_offset_x + pos2.x, noise_offset_y + pos2.y);

      let hilo = p5.Vector.sub(pos1, pos2)
        .normalize()
        .mult(val1 - val2);

      mean_arrow.add(hilo);
    }
    mean_arrow.div(radial_samples);
    mean_arrow.rotate(p.PI / 2);
    return mean_arrow;
  }

  function display_flow() {
    for (let i = 0; i < flow_grid.length; i++) {
      for (let j = 0; j < flow_grid[i].length; j++) {
        if (
          flow_grid[i][j].mag() > 0.002 &&
          (!circular_shape ||
            inside_radius(
              i - flow_grid.length / 2,
              j - flow_grid[i].length / 2,
              450 / flow_cell_size
            ))
        ) {
          p.line(
            j * flow_cell_size,
            i * flow_cell_size,
            j * flow_cell_size + flow_grid[i][j].x * flow_cell_size * 2500,
            i * flow_cell_size + flow_grid[i][j].y * flow_cell_size * 2500
          );
        }
      }
    }
  }

  function inside_radius(x, y, r) {
    return p.sqrt(p.pow(x, 2) + p.pow(y, 2)) < r;
  }

};
onMounted(() => {
  let parts = document.URL.split('/')
  if (parts[parts.length - 1] === 'hedgehog') {
    document.getElementById('hedgehog').classList.add('active');
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