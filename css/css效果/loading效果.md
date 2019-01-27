```
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
    <style>
        .loaders .spinner {
            -webkit-animation: rotator 1.4s linear infinite;
            -moz-animation: rotator 1.4s linear infinite;
            -o-animation: rotator 1.4s linear infinite;
            -ms-animation: rotator 1.4s linear infinite;
            animation: rotator 1.4s linear infinite;
        }

        .loading {
            position: absolute;
            left: 50%;
            top: 50%;
            padding: 0;
            margin-left: -45px;
            margin-top: -45px;
            width: 90px;
            height: 90px;
            overflow: hidden;
        }

        .loaders .path {
            stroke-dasharray: 187;
            stroke-dashoffset: 93.5;
            stroke: #3899ec;
            transform-origin: center;
            animation: dash 1.4s ease-in-out infinite, colors 5.6s ease-in-out infinite;
        }

        @keyframes rotator {
            0% {
                -webkit-transform: rotate(0);
                -moz-transform: rotate(0);
                -o-transform: rotate(0);
                -ms-transform: rotate(0);
                transform: rotate(0)
            }

            100% {
                -webkit-transform: rotate(270deg);
                -moz-transform: rotate(270deg);
                -o-transform: rotate(270deg);
                -ms-transform: rotate(270deg);
                transform: rotate(270deg)
            }
        }


        @keyframes colors {
            0% {
                stroke: #3899ec
            }

            25% {
                stroke: #42c5bf
            }

            50% {
                stroke: #6ccc65
            }

            75% {
                stroke: #79b6f0
            }

            100% {
                stroke: #17a8a4
            }
        }

        @keyframes dash {
            0% {
                stroke-dashoffset: 187
            }

            50% {
                stroke-dashoffset: 46.75;
                -webkit-transform: rotate(135deg);
                -moz-transform: rotate(135deg);
                -o-transform: rotate(135deg);
                -ms-transform: rotate(135deg);
                transform: rotate(135deg)
            }

            100% {
                stroke-dashoffset: 187;
                -webkit-transform: rotate(450deg);
                -moz-transform: rotate(450deg);
                -o-transform: rotate(450deg);
                -ms-transform: rotate(450deg);
                transform: rotate(450deg)
            }
        }
    </style>


</head>

<body>
    <div class="loaders">
        <div class="loading">
            <spinner>
                <svg width="90px" height="90px" viewBox="0 0 66 66" xmlns="http://www.w3.org/2000/svg" class="spinner">
                    <circle fill="none" stroke-width="2" stroke-linecap="round" cx="33" cy="33" r="30" class="path"></circle>
                </svg>
            </spinner>
        </div>
    </div>
</body>

</html>
```
