# RT
<!DOCTYPE html>

<head>
    <style>
        .center{
            margin: auto;
            display: flex;
            align-items: center;
        }
        .border{
            border: 1px solid #555555;
        }
    </style>
</head>

<body style = "background-color: #000000;">
    <div class = "center">
        <canvas id = "canvas" class = "border" width = 600 height = 600 style = "margin: auto;"></canvas>
    </div>
</body>

<script>
    let canvas = document.getElementById("canvas");
    let context = canvas.getContext("2d");

    let viewportSize = 1;

    function Draw(x, y, color){
        x = canvas.width/2 + x;
        y = canvas.height/2 - y;
        color = "rgba("+color[0]+","+color[1]+","+color[2]+","+color[3]/255+")";
        context.fillStyle = color;
        context.fillRect(x,y,1,1);
    }

    // Screen Space
    function CanvasToViewport(x, y){
        return [
            x * viewportSize / canvas.width,
            y * viewportSize / canvas.height,
            viewportSize
        ];
    }

    function InRange(n, min, max){
        return ( n >= min && n <= max );
    }

    let red = [255, 50, 50, 255];
    let black = [0, 0, 0, 255];
    let grey = [150, 150, 150, 255];
    let blue = [50, 50, 255, 255];
    let green = [50, 255, 50 ,255];

    let purple = [87, 95, 207, 255];
    let orange = [255, 94, 87, 255];
    let mint = [11, 232, 129, 255]
    
    // Vector math
    function Dot(v1,v2){
        return v1[0] * v2[0] + v1[1] * v2[1] + v1[2] * v2[2];
    }
    function Minus(v1, v2){
        return [ v1[0] - v2[0], v1[1] - v2[1], v1[1] - v2[2] ];
    }
    function MultConst(n, v1){
        return [ v1[0] * n, v1[1] * n, v1[2] * n ];
    }

    // Object instance
    class ball {
        constructor(p, r, c){
            this.p = p;
            this.r = r;
            this.color = c;
        }
    }

    let INSTANCES = [
        new ball([0, 0, 20], 5, orange),
        new ball([-2, 2, 5], 2, purple),
        new ball([2, 2, 5], 1, mint),
    ];

    function GetIntersects(o, d, obj){
        let r = obj.r;
        let oc = Minus(o, obj.p);

        let a = Dot(d, d);
        let b = 2 * Dot(oc, d);
        let c = Dot(oc, oc) - (r*r);

        let di = (b*b) - (4*a*c);
        if (di < 0) {
            return [Infinity, Infinity];
        }

        let p1 = ( (-b + Math.sqrt(di)) / (2*a) );
        let p2 = ( (-b - Math.sqrt(di)) / (2*a) );
        return [p1, p2];
    }

    function Ray(o, d, dMin, dMax){
        let leastDistance = Infinity;
        let nColor = black;
        for (let obj of INSTANCES){
            let p = GetIntersects(o, d, obj);
            if (InRange(p[0], dMin, dMax) && p[0] < leastDistance){
                leastDistance = p[0];
                nColor = obj.color;
            } else if (InRange(p[1], dMin, dMax) && p[1] < leastDistance){
                leastDistance = p[1];
                nColor = obj.color;
            }
        }
        return nColor;
    }

    function main(){
        let cameraPosition = [0, 0, 0];
        for (let x = -canvas.width/2; x < canvas.width/2; x++){
            for (let y = -canvas.height/2; y < canvas.height/2; y++){
                let direction = CanvasToViewport(x, y);
                let color = Ray(cameraPosition, direction, 1, Infinity);
                Draw(x, y, color);
            }
        }
    }
    main();

</script>
