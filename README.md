# html5-canvas
moving object in html canvas geting form json


<!DOCTYPE html>
<html>
<head>

    <style type="text/css">
    	body {
    		background-color: ivory;
    	}
    	canvas {
    		border:1px solid red;
            position:absolute;
            left:80px;
            top:80px;
        }</style>
    </head>

    <body>
    	<p id="downlog">Down</p>
    	<p><button id="start">start</button>
            <select id="building">Select building
                <option value="#1">Building1</option>
                <option value="#2">Building2</option>
                <option value="#3">Building3</option>
                <option value="#4">Building4</option>
            </select></p>
            <canvas id="canvas" style="z-index:1;" width=1000 height=700></canvas>
            <canvas id="canvas2" style="z-index:2;" width=1000 height=700></canvas>
            <canvas id="canvas3" style="z-index:3;" width=1000 height=700></canvas>
            <canvas id="canvas4" style="z-index:4;" width=1000 height=700></canvas>
            <canvas id="canvas5" style="z-index:5;" width=1000 height=700></canvas>



            <script type="text/javascript" src="https://code.jquery.com/jquery-1.9.1.js"></script>
            <script type="text/javascript">




                var canvas = document.getElementById("canvas");
                var canvasbuilding2 = document.getElementById("canvas2");
                var canvasbuilding3 = document.getElementById("canvas3");
                var ctx = canvas.getContext("2d");
                var buildctx = canvasbuilding2.getContext("2d");
                buildctx = canvasbuilding3.getContext("2d");
                var canvasOffset = $("#canvas").offset();
                var offsetX = canvasOffset.left;
                var offsetY = canvasOffset.top;

        // animation variables
        var currentX = 500;
        var currentY = 500;
        var frameCount = 60;
        var timer;
        var points;
        var currentFrame;
        




        function animate() {
            var point = points[currentFrame++];
            draw(point.x, point.y);

            // refire the timer until out-of-points
            if (currentFrame < points.length) {
                timer = setTimeout(animate, 1000 / 60);
            }
        }

        function linePointsDirection(x1, y1, x2, y2, frames) {

            console.log(x1, y1, x2, y2, frames)
            var dx = x2 - x1;
            var dy = y2 - y1;

           var incrementX = dx / frames;

           var incrementY = dy / frames;
           var a = new Array();

           a.push({
            x: x1,
            y: y1
        });

           for (var frame = 0; frame < frames - 1; frame++) {
                //console.log(incrementX);
               // console.log(frame)
               console.log(y1 + incrementY* frame)
                //console.log(y1)
                console.log(frame)
                if(frame<=50){
                    console.log('x')
                    var yIN=y1+ (incrementY * frame);
                    a.push({
                        x: x1 ,
                        y: yIN
                    });
                }else{
                    a.push({
                        x: x1+ (incrementX * frame) ,
                        y: yIN
                    });
                }
                
            }
            a.push({
                x: x2,
                y: y2
            });

           return (a);
       }

       function draw(x, y) {
        ctx.clearRect(0, 0, canvas.width, canvas.height);
        ctx.beginPath();
        ctx.fillStyle = "skyblue";
        ctx.strokeStyle = "gray";
        ctx.rect(x, y, 30, 20);
        ctx.fill();
        ctx.rotate(Math.PI / 2);
        ctx.stroke();
    }

    function drawBuilding(x, y, hi, wi, color, location) {
          //  buildctx.clearRect(0, 0, canvas.width, canvas.height);
          buildctx.beginPath();
          buildctx.fillStyle = color;
          buildctx.strokeStyle = "yellow";
          buildctx.font="16px Georgia";
          buildctx.fillText(location,x,y);
          buildctx.rect(x, y, hi, wi);
          buildctx.fill();
          buildctx.stroke();


      }

      function handleMouseDown(e) {
        mouseX = parseInt(e.clientX - offsetX);
        mouseY = parseInt(e.clientY - offsetY);
        $("#downlog").html("Down: " + mouseX + " / " + mouseY);

            // Put your mousedown stuff here
            points = linePoints(currentX, currentY, mouseX, mouseY, frameCount);
            currentFrame = 0;
            currentX = mouseX;
            currentY = mouseY;
            animate();
        }

        function handleStart(e, x, y) {
            mouseX = x;
            mouseY = y;
            $("#downlog").html("Down: " + mouseX + " / " + mouseY);

            // Put your mousedown stuff here
            points = linePointsDirection(currentX, currentY, mouseX, mouseY, frameCount);
            currentFrame = 0;
            animate();
        }

        $("#start").click(function(e){
            var bulding_number=$("#building").val();
            $.ajax({url: "building.json", success: function(result){
                var build=$.grep(result, function(obj){
                    return obj.building == bulding_number
                });
                handleStart(e, build[0].x, build[0].y)  
            }});


        })
        draw(500, 600);
        $(document).ready(function(){
           $.ajax({url: "building.json", success: function(result){
            $.each(result,function(result, val){
                drawBuilding(val.x, val.y, 50, 50, val.color, val.location)
            })
        }});
       })

    </script>
</body>
</html>
