<script setup>
import p5 from "p5"
import { onMounted, onUnmounted } from 'vue'

const sketch = function (p) {
  let width = 800;
  let height = 800;
  if (window.innerWidth <= 1800) {
    width = 700;
    height = 700;
  }

  var deg_to_rad = Math.PI / 180.0;

  let breeze = 0
  let wind = 0

  let tree = ""

  var i = 0;
  var change = 0;

  let cv = 0;
  let isBlossom;

  if (p.random(0, 1) > 0.75) {
    isBlossom = true;
  } else {
    isBlossom = false
  }

  let blossomColor = p.random([[180, 200, 240], [240, 190, 205], [242, 189 , 100] ])

  p.setup = function () {
    p.createCanvas(width, height);
    p.pixelDensity(2048 / width);    p.colorMode(p.RGB);
    tree = new Tree(width / 2, height / 1.25, p.random(2.7, 3.1), [50 + p.random(cv * -1, cv), 40 + p.random(cv * -1, cv), 30 + p.random(cv * -1, cv)], isBlossom, blossomColor)

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
      p.saveCanvas('ft_' + Math.floor(p.random(100000000, 999999999)))
    })
  };

  p.draw = function () {
    p.background(220, 240, 210);
    p.frameRate(30)
    tree.renderTree();
    
    breeze += Math.abs(wind) * 0.05 + 0.1;
    if (breeze > 2 * p.PI) {
      breeze -= 2 * p.PI
    }

    change += p.random(-1, 1);
    change = change * 0.9;

    i += change / 200;
    i = i * 0.8;
    wind = i * 3;
  };

  function drawLine(x1, y1, x2, y2, color, width) {
    p.stroke(color[0], color[1], color[2]);
    if (width > 2) {
      p.strokeWeight(width);
    } else {
      p.strokeWeight(2);
    }
    p.line(x1, y1, x2, y2);
  }

  function Tree(x1, y1, seedSize, color, blossom, blossomColor) {

    function Leaf(relativePos, relativeAngle, size, color) {

      this.size = size;
      this.color = color;
      this.offset = p.random()

      this.relativePos = relativePos;
      this.relativeAngle = relativeAngle;

      this.render = function (x1, y1) {

        var angle = this.relativeAngle;

        if (wind > 0) {
          angle += (0 - angle) * (Math.pow(wind, 2) / (1 + Math.pow(wind, 2)));
        } else if (wind < 0) {
          angle += (-180 - angle) * (Math.pow(wind, 2) / (1 + Math.pow(wind, 2)));
        }
        angle += Math.sin(breeze) * 10 + Math.sin(breeze * 3) * 25 / this.size;

        let sizeFactor
        if (blossom) {
          sizeFactor = 4
        } else {
          sizeFactor = this.size * 1.3;
        }

        var x2 = x1 + (Math.cos((angle + 15) * deg_to_rad) * sizeFactor * 2);
        var y2 = y1 + (Math.sin((angle + 15) * deg_to_rad) * sizeFactor * 2);

        var x3 = x1 + (Math.cos((angle + 1) * deg_to_rad) * (sizeFactor * 2 + 10));
        var y3 = y1 + (Math.sin((angle + 1) * deg_to_rad) * (sizeFactor * 2 + 10));

        var x4 = x1 + (Math.cos((angle - 15) * deg_to_rad) * sizeFactor * 2);
        var y4 = y1 + (Math.sin((angle - 15) * deg_to_rad) * sizeFactor * 2);

        if (blossom) {
          p.fill(blossomColor)
          p.noStroke()
          p.circle(x4, y4, 8)
          p.circle(x2, y2, 8)
          p.circle(x3, y3, 8)
          p.fill('white')
          p.circle(x1, y1, 8)
        } else {

          let color = this.color

          p.fill(color)
          p.noStroke()
          p.quad(x1, y1, x2, y2, x3, y3, x4, y4)

        }

      }

    }


    function Branch(relativePos, relativeAngle, size, length, color) {

      this.size = size;
      this.length = length;

      if (length < 3) {
        this.length = 3
      }

      this.color = color;

      this.relativePos = relativePos;
      this.relativeAngle = relativeAngle;

      this.contents = []

      if (!(this.size < 4)) {

        this.contents.push(

          new Branch(
            "end",
            this.relativeAngle - p.random(5, 25),
            size / 1.2 - p.random(1.5, 1.7),
            length / p.random(1, 1.2),
            [color[0] + p.random(0, 10), color[1] + p.random(0, 10), color[2] + p.random(0, 5)]
          ),

          new Branch(
            "end",
            this.relativeAngle + p.random(5, 25),
            size / 1.2 - p.random(1.5, 1.7),
            length / p.random(1, 1.2),
            [color[0] + p.random(0, 10), color[1] + p.random(0, 10), color[2] + p.random(0, 5)]
          )

        )

        if (p.random() > 0.5) {

          this.contents.push(

            new Branch(
              "mid",
              this.relativeAngle + p.random(-60, 60),
              size - 8,
              length - 5,
              [color[0] + p.random(0, 10), color[1] + p.random(0, 10), color[2] + p.random(0, 5)]
            )

          )
        }
      } else {

        let rand = p.random(60, 120);

        this.contents.push(new Leaf("mid", this.relativeAngle + p.random(-45, 45), p.random(5, 12), [rand, rand + 50, rand - 20]))
        this.contents.push(new Leaf("end", this.relativeAngle + p.random(-45, 45), p.random(4, 12), [rand, rand + 50, rand - 20]))
        this.contents.push(new Leaf("end", this.relativeAngle + p.random(-45, 45), p.random(3, 12), [rand, rand + 50, rand - 20]))
        this.contents.push(new Leaf("mid", this.relativeAngle + p.random(-45, 45), p.random(2, 12), [rand, rand + 50, rand - 20]))

      }

      this.render = function (x1, y1) {

        var angle = this.relativeAngle;

        if (wind > 0) {
          angle += (0 - angle) * (Math.pow(wind, 2) / (1 + Math.pow(wind, 2))) / (Math.ceil(size) * 0.5);
        } else if (wind < 0) {
          angle += (-180 - angle) * (Math.pow(wind, 2) / (1 + Math.pow(wind, 2))) / (Math.ceil(size) * 0.5);
        }
        angle += Math.sin(breeze) * 400 / Math.pow(this.length, 2);

        var x2 = x1 + (Math.cos(angle * deg_to_rad) * this.length * 3);
        var y2 = y1 + (Math.sin(angle * deg_to_rad) * this.length * 3);
        drawLine(x1, y1, x2, y2, this.color, this.size);

        this.contents.forEach(element => {

          if (element.relativePos == "end") {
            element.render(x2, y2);
          } else {
            element.render((x1 + x2) / 2, (y1 + y2) / 2);
          }
        });
      }

    }

    this.basex = x1;
    this.basey = y1;
    this.seed = seedSize;
    this.color = color;
    this.angle = -90;
    this.size = Math.pow(this.seed, 3);
    this.length = Math.pow(this.seed, 3);
    this.blossom = blossom
    this.blossomColor = blossomColor

    this.root = new Branch("end", this.angle, this.size, this.length, this.color)

    this.renderTree = function () {

      p.fill(0, 0, 0, 50);
      p.noStroke();
      p.ellipse(this.basex, this.basey + 10, Math.pow(this.seed, 3) * 10, Math.pow(this.seed, 3));

      p.fill(60, 50, 40);
      p.rectMode(p.RADIUS);
      p.rect(this.basex, this.basey, Math.pow(this.seed, 3) / 2, Math.pow(this.seed, 3) / 2, 5);

      this.root.render(this.basex, this.basey)

    }

  }

};
onMounted(() => {
  let parts = document.URL.split('/')
  if (parts[parts.length - 1] === 'fractal') {
    document.getElementById('fractal').classList.add('active');
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