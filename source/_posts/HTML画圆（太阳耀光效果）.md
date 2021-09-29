---
title: HTML画圆（太阳耀光效果）
date: 2021-01-15
---



- 效果  自己改下颜色

![Snipaste_2021-01-15_09-03-17.png](http://file.ingrun.cn/Snipaste_2021-01-15_09-03-17.png)




```html
<!DOCTYPE html>
<html lang="en">

<head>
  <title>TweetShirt</title>
  <meta charset="utf-8">


  <style type="text/css">
    canvas {
      border: 1px solid black;
    }
  </style>


  <script>
    window.onload = function () {
      var button = document.getElementById("previewButton");
      button.onclick = previewHandler;
      function previewHandler() {
        var canvas = document.getElementById("tshirtCanvas");
        var context = canvas.getContext("2d");
        fillBackgroundColor(canvas, context);

        var selectObj = document.getElementById("shape");
        var index = selectObj.selectedIndex;
        var shape = selectObj[index].value;
        //判断是方形还是圆形
        if (shape == "squares") {
          for (var squares = 10; squares < 12; squares++) {
            drawSquare(canvas, context);
          }
        } else if (shape == "circles") {
          for (var circles = 0; circles < 20; circles++) {
            drawCircle(canvas, context);
          }
        }
      }

      function fillBackgroundColor(canvas, context) {
        var selectObj = document.getElementById("backgroundColor");
        var index = selectObj.selectedIndex;
        var bgColor = selectObj.options[index].value;
        context.fillStyle = bgColor;
        context.fillRect(0, 0, canvas.width, canvas.height);
      }

      //封装方形
      function drawSquare(canvas, context) {
        var w = Math.floor(Math.random() * 40);
        var x = Math.floor(Math.random() * canvas.width);
        var y = Math.floor(Math.random() * canvas.height);

        context.fillStyle = "blue";
        context.fillRect(x, y, w, w);
      }

      //封装圆形
      function drawCircle(canvas, context) {
        var radius = Math.floor(Math.random() * 40);
        var x = Math.floor(Math.random() * canvas.width);
        var y = Math.floor(Math.random() * canvas.height);

        context.beginPath();
        context.arc(x, y, radius, 0, degreesToRadians(360), true);
        context.fillStyle = "rgba(0,0,0, 0.6)";
        context.fill();
      }

      function degreesToRadians(degrees) {
        return (degrees * Math.PI) / 180;
      }

    }
  </script>
</head>

<body>
  <canvas id="tshirtCanvas" width="600" height="200">
    <p>Please update your brower to use TweetShirt</p>
  </canvas>
  <form>
    <p>
      <label for="backgroundColor">Select background color:</label>
      <select id="backgroundColor">
        <option value="white" selected="selected">White</option>
        <option value="black">Black</option>
      </select>
    </p>
    <p>
      <label for="shape">Circles or squares?</label>
      <select id="shape">
        <option value="none" selected="selected">Neither</option>
        <option value="circles">Circles</option>
        <option value="squares">squares</option>
      </select>
    </p>
    <p>
      <input type="button" name="previewButton" id="previewButton" value="Preview">
    </p>
  </form>
</body>

</html>
```

