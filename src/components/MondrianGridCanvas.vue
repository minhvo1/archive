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
  let size = window.innerWidth;

  let stepSize = p.random(5, 40);
  let step = size / stepSize
  let white = '#F2F5F1';
  let palette = {
    one: ['#D40920', '#1356A2', '#F7D842', '#30303a'],
    two:['#D40920', '#1356A2', '#F7D842'],
    three: ['#D40920','#30303a'],
    four:['#1356A2', '#F7D842']
  }
  let colorPalette = palette[Object.keys(palette)[Math.floor(Math.random() * Object.keys(palette).length)]]

  let squares = [{
    x: 0,
    y: 0,
    width: size,
    height: size
  }];


  p.setup = function() {
    p.createCanvas(width, height);
    p.strokeWeight(5)

    p.frameRate(120);
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
      p.saveCanvas('mg_' + Math.floor(p.random(100000000, 999999999)))
    })

  };
  
  p.draw = function() {
  for (let i = 0; i < squares.length; i++) {
    p.beginShape();
    p.rect(
      squares[i].x,
      squares[i].y,
      squares[i].width,
      squares[i].height
    );

    if(p.random(1, 5) >= p.random(3, 3.5)) {
      p.fill(colorPalette[Math.floor(Math.random() * colorPalette.length)])
    } else {
      p.fill(white)
    }

    if (stepSize >= 25) {
      p.strokeWeight(10)
    } else {
      p.strokeWeight(12)
    }
  }
  p.noLoop()
  };

  function splitSquaresWith(coordinates) {
  const { x, y } = coordinates;

  for (let i = squares.length - 1; i >= 0; i--) {
  const square = squares[i];

  if (x && x > square.x && x < square.x + square.width) {
      if(Math.random() > 0.5) {
        squares.splice(i, 1);
        splitOnX(square, x); 
      }
  }

  if (y && y > square.y && y < square.y + square.height) {
      if(Math.random() > 0.5) {
        squares.splice(i, 1);
        splitOnY(square, y); 
      }
  }
  }
}

function splitOnX(square, splitAt) {
  let squareA = {
    x: square.x,
    y: square.y,
    width: square.width - (square.width - splitAt + square.x),
    height: square.height
  };

  let squareB = {
  x: splitAt,
  y: square.y,
  width: square.width - splitAt + square.x,
  height: square.height
  };

  squares.push(squareA);
  squares.push(squareB);
}

function splitOnY(square, splitAt) {
  let squareA = {
    x: square.x,
    y: square.y,
    width: square.width,
    height: square.height - (square.height - splitAt + square.y)
  };

  let squareB = {
  x: square.x,
  y: splitAt,
  width: square.width,
  height: square.height - splitAt + square.y
  };

  squares.push(squareA);
  squares.push(squareB);
}

for (let i = 0; i < size; i += step) {
  splitSquaresWith({ y: i });
  splitSquaresWith({ x: i });
}

};
onMounted(() => {
  let parts = document.URL.split('/')
  if (parts[parts.length - 1] === 'mondrian') {
    document.getElementById('mondrian').classList.add('active');
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