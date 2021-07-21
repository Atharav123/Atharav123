button {
  background: $color-text;
  border: 10px solid $color-green;
  cursor: pointer;
  display: block;
  font-family: monospace;
  font-size: 24px;
  height: 80px;
  left: 10%;
  line-height: 60px;
  margin: 0;
  outline: none;
  padding: 0 1.2em;
  position: absolute;
  text-align: right;
  top: 260px;
  width: 80%;

  &:hover {
    background: $color-green;
  }
}

input[type=checkbox] + label:before {
  background: $color-text;
  content: '';
@@ -76,9 +53,39 @@ input[type=checkbox]:checked + label:before {
  margin-left: 0;
}

button {
  background: $color-text;
  border: 10px solid $color-green;
  cursor: pointer;
  display: block;
  font-family: monospace;
  font-size: 24px;
  height: 80px;
  line-height: 60px;
  margin: 0;
  outline: none;
  padding: 0 1.2em;
  text-align: right;

  &:hover {
    background: $color-green;
  }

  &#deleteme {
    margin: 2rem;
  }
}

.scene {
  margin: 0;
  padding: 0;

  button {
    left: 10%;
    top: 260px;
    width: 80%;
    position: absolute;
  }
}

.fill {
 50  examples/pages/destroy.html 
@@ -0,0 +1,50 @@
<!DOCTYPE html>
<html>
<head>

	<meta charset="utf-8">

	<title>Parallax.js | Instance Destruction Example</title>

	<!-- Behavioral Meta Data -->
	<meta name="apple-mobile-web-app-capable" content="yes">
	<meta name="viewport" content="width=device-width, initial-scale=1, maximum-scale=1, user-scalable=no">

	<!-- Styles -->
	<link rel="stylesheet" type="text/css" href="styles.css"/>

</head>
<body>

	<button id="deleteme">Delete me!</button>

	<div id="container" class="container">
		<div id="scene" class="scene">
			<div data-depth="1.00"><img src="images/layer1.png"></div>
			<div data-depth="0.80"><img src="images/layer2.png"></div>
			<div data-depth="0.60"><img src="images/layer3.png"></div>
			<div data-depth="0.40"><img src="images/layer4.png"></div>
			<div data-depth="0.20"><img src="images/layer5.png"></div>
			<div data-depth="0.00"><img src="images/layer6.png"></div>
		</div>
	</div>

	<!-- Scripts -->
	<script src="./parallax.js"></script>
	<script>

	var scene = document.getElementById('scene');
	var parallax = new Parallax(scene);

	document.getElementById('deleteme').onclick = function() {
		parallax.destroy();
		parallax = null;

		this.onclick = null;
		this.style.display = 'none';
	};

	</script>

</body>
</html>
  20  src/parallax.js 
@@ -156,7 +156,6 @@ class Parallax {
  constructor(element, options) {

    this.element = element
    this.layers = element.getElementsByClassName('layer')

    const data = {
      calibrateX: helpers.data(this.element, 'calibrate-x'),
@@ -349,11 +348,11 @@ class Parallax {
    if (this.orientationSupport) {
      this.portrait = false
      window.addEventListener('deviceorientation', this.onDeviceOrientation)
      setTimeout(this.onOrientationTimer, this.supportDelay)
      this.detectionTimer = setTimeout(this.onOrientationTimer, this.supportDelay)
    } else if (this.motionSupport) {
      this.portrait = false
      window.addEventListener('devicemotion', this.onDeviceMotion)
      setTimeout(this.onMotionTimer, this.supportDelay)
      this.detectionTimer = setTimeout(this.onMotionTimer, this.supportDelay)
    } else {
      this.calibrationX = 0
      this.calibrationY = 0
@@ -570,6 +569,21 @@ class Parallax {
    }
  }

  destroy() {
    this.disable()

    clearTimeout(this.calibrationTimer)
    clearTimeout(this.detectionTimer)

    this.element.removeAttribute('style')
    for (let index = 0; index < this.layers.length; index++) {
      this.layers[index].removeAttribute('style')
    }

    delete this.element
    delete this.layers
  }
