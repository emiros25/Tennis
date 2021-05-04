# Tennis
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8"/>
    <style>
        #minCanvas{
            border:20px solid #c2b280;
            border-right:none;
            border-top:none;
        }
        #divPoeng{
            width: 580px;
            background-color:#c2b280;
            height: 30px;
            line-height: 30px;
            padding-left: 20px;
            padding-right: 20px;
        }
        #hdnPoeng, #hdnRekord{
            font-size: 20px;
            color:white;
            font-family: arial;
            margin: 0;
        }
        #hdnPoeng{
            float: left;
        }
        #hdnRekord{
            float: right;
        }
    </style>
</head>
<body>
    <div id="divPoeng">
        <h2 id="hdnPoeng">Poeng:0</h2>
        <h2 id="hdnRekord">Rekord:0</h2>
    </div>
    <canvas width="600"height="400"id="minCanvas"></canvas>
    <script>
        //DEL 1 - SELVE SKALLET
        var minCanvas = document.getElementById("minCanvas");
        var ctx = minCanvas.getContext("2d");
        var theGameIsOn = true;
        var hdnPoeng = document.getElementById("hdnPoeng");
        var hdnRekord = document.getElementById("hdnRekord");
        var poeng = 0;
        if(localStorage.rekord===undefined){
            localStorage.rekord=0;
        }
        hdnRekord.innerHTML = "Rekord: "+localStorage.rekord;
        // DEL 2
        var bane ={
            bredde:minCanvas.width,
            hoyde:minCanvas.height,
            gressfarge:"DarkGreen",
            linjefarge:"White",
            linjetykkelse:4
        };
        function tegnBane(){
           ctx.fillStyle=bane.gressfarge;
            ctx.fillRect(0,0,bane.bredde,bane.hoyde);
            ctx.fillStyle=bane.linjefarge;
            ctx.fillRect(bane.bredde/2-bane.linjetykkelse/2,0,bane.linjetykkelse,bane.hoyde);

        }
        //DEL 3
        var ball={
            radius:7,
            xpos:100,
            ypos:100,
            farge:"yellow",
            xretning:-1,
            yretning:1,
            xfart:4,
            yfart:4
        }
        function tegnBall(){
            ctx.beginPath();
            ctx.arc(ball.xpos,ball.ypos,ball.radius,0,Math.PI*2);
            ctx.closePath();
            ctx.fillStyle = ball.farge;
            ctx.fill();
            ball.xpos=ball.xpos+ball.xfart*ball.xretning;
            ball.ypos=ball.ypos+ball.yfart*ball.yretning;
        }
        var racket ={
            "bredde":10,
            "hoyde":50,
            "farge":"White",
            "xpos":bane.bredde-15,
            "ypos":bane.hoyde/2,
            "yretning":0,
            "yfart":5
        };
        function tegnRacket(){
            ctx.fillStyle = racket.farge;
            ctx.fillRect(racket.xpos,racket.ypos,racket.bredde,racket.hoyde);
            if(racket.ypos<=0 && racket.yretning===-1){
                return;
            }
            if(racket.ypos+racket.hoyde>=bane.hoyde && racket.yretning===1){
                return;
            }
            racket.ypos = racket.ypos+racket.yfart*racket.yretning;
        };
        
        function sjekkOmBallTrefferVegg(){
            if(ball.xpos<=ball.radius){
                ball.xretning = 1;
            }
            if(ball.ypos+ball.radius>=bane.hoyde){
                ball.yretning =-1;
            }
            if(ball.ypos<=ball.radius){
                ball.yretning = 1;
            }
        }
        function sjekkOmBallTrefferRacket(){
            var ballenErTilVenstre = ball.xpos + ball.radius < racket.xpos;
            var ballenErTilHoyre = ball.xpos - ball.radius > racket.xpos + racket.bredde;
            var ballenErOver = ball.ypos + ball.radius < racket.ypos;
            var ballenErUnder = ball.ypos - ball.radius > racket.ypos + racket.hoyde;
            if(!ballenErTilVenstre&&!ballenErTilHoyre&&!ballenErOver&&!ballenErUnder){
            ball.xretning =-1;
                poeng=poeng+1;
                hdnPoeng.innerHTML="poeng: "+poeng;
                if(poeng>localStorage.rekord){
                    localStorage.rekord = poeng;
                    hdnRekord.innerHTML = "Ny rekord: "+poeng;
                    hdnRekord.style.color="Green";
                }
            }
        }
        function sjekkOmBallErUtenforBanen(){
            if(ball.x>bane.bredde+ball.radius*2){
                theGameIsOn = false;
            }
        }
        function gameLoop(){
            tegnBane()
            tegnBall()
            tegnRacket()
            sjekkOmBallTrefferVegg()
            sjekkOmBallTrefferRacket()
            sjekkOmBallErUtenforBanen()
            if(theGameIsOn){
                requestAnimationFrame(gameLoop);
            }
        }
        gameLoop();
        document.onkeydown = function(evt){
            var tastekode = evt.keyCode;
            if(tastekode === 38){
                racket.yretning = -1;
            }
            if(tastekode=== 40){
                racket.yretning = 1;
            }
        }
        document.onkeyup = function(evt){
            var tastekode = evt.keyCode;
            if(tastekode === 38 &&racket.yretning===-1){
                racket.yretning = 0;
            }
            if(tastekode===40&&racket.yretning===1){
                racket.yretning = 0;
            }
        }
        
        
    </script>
</body>
</html>
