<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Face Detection</title>
    <script defer src="https://cdn.jsdelivr.net/npm/@tensorflow/tfjs"></script>
    <script defer src="https://cdn.jsdelivr.net/npm/face-api.js"></script>
    <style>
        body { text-align: center; font-family: Arial, sans-serif; }
        video { border: 2px solid black; }
        canvas { position: absolute; top: 0; left: 0; }
    </style>
</head>
<body>

    <h2>Face Detection with Face-api.js</h2>
    <video id="video" width="640" height="480" autoplay></video>
    <canvas id="overlay"></canvas>

    <script>
        const video = document.getElementById('video');

        async function startVideo() {
            try {
                const stream = await navigator.mediaDevices.getUserMedia({ video: true });
                video.srcObject = stream;
            } catch (error) {
                console.error("Error accessing webcam:", error);
            }
        }

        async function loadModels() {
            await faceapi.nets.tinyFaceDetector.loadFromUri('https://justadudewhohacks.github.io/face-api.js/models/');
            startVideo();
        }

        video.addEventListener('play', async () => {
            const canvas = document.getElementById('overlay');
            const displaySize = { width: video.width, height: video.height };
            faceapi.matchDimensions(canvas, displaySize);

            setInterval(async () => {
                const detections = await faceapi.detectAllFaces(video, new faceapi.TinyFaceDetectorOptions());
                canvas.getContext('2d').clearRect(0, 0, canvas.width, canvas.height);
                faceapi.draw.drawDetections(canvas, faceapi.resizeResults(detections, displaySize));
            }, 100);
        });

        loadModels();
    </script>

</body>
</html>
