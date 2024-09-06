<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Modified Page</title>
    <style>
        /* Styling the Start Recording button */
        #startButton {
            background-color: red;
            color: white;
            padding: 10px 20px;
            border: none;
            cursor: pointer;
            margin: 10px 0;
        }

        /* Styling the Share Camera with Code button */
        #shareButton {
            background-color: blue;
            color: white;
            padding: 10px 20px;
            border: none;
            cursor: pointer;
            margin-top: 10px;
        }

        /* Styling the Save Recording button */
        #saveButton {
            background-color: green;
            color: white;
            padding: 10px 20px;
            border: none;
            cursor: pointer;
            margin-top: 10px;
        }

        /* Centering elements */
        .header {
            text-align: center;
            font-size: 24px;
            margin: 20px 0;
            font-weight: bold;
        }

        #camera {
            text-align: center;
            margin-top: 20px;
        }

        /* Styling the code display */
        #codeDisplay {
            font-size: 20px;
            margin-top: 10px;
            font-weight: bold;
        }

        /* Styling the video element like YouTube UI */
        #video {
            border: 1px solid #ccc;
            border-radius: 4px;
            width: 320px;
            height: 240px;
            background: black;
        }

        /* Styling the timer display */
        #timerDisplay {
            font-size: 20px;
            font-weight: bold;
            margin-top: 10px;
        }

        /* Red blinking dot for recording effect */
        #recIcon {
            color: red;
            font-weight: bold;
            display: none;
            animation: blink 1s infinite;
        }

        @keyframes blink {
            50% { opacity: 0; }
        }
    </style>
</head>
<body>
    <div class="header">[Made by @mrxzrox]</div>

    <div id="camera">
        <video id="video" width="320" height="240" autoplay></video>
        <button id="startButton">Start Recording</button>
        <button id="shareButton">Share Camera with Code</button>
        <button id="saveButton">Save Recording</button>
        <div id="timerDisplay"></div>
        <div id="codeDisplay"></div>
        <div id="recIcon">REC ●</div>
    </div>

    <script>
        let video = document.getElementById('video');
        let startButton = document.getElementById('startButton');
        let shareButton = document.getElementById('shareButton');
        let saveButton = document.getElementById('saveButton');
        let timerDisplay = document.getElementById('timerDisplay');
        let codeDisplay = document.getElementById('codeDisplay');
        let recIcon = document.getElementById('recIcon');

        navigator.mediaDevices.getUserMedia({ video: true })
            .then(stream => {
                video.srcObject = stream;
            })
            .catch(err => {
                console.error("Error accessing webcam: " + err);
            });

        let mediaRecorder;
        let recordedChunks = [];
        let countdownTimer;

        startButton.addEventListener('click', () => {
            startRecording();
        });

        shareButton.addEventListener('click', () => {
            shareCamera();
        });

        saveButton.addEventListener('click', () => {
            saveRecording();
        });

        function startRecording() {
            recordedChunks = [];
            let stream = video.srcObject;
            mediaRecorder = new MediaRecorder(stream);

            mediaRecorder.ondataavailable = function(event) {
                if (event.data.size > 0) {
                    recordedChunks.push(event.data);
                }
            };

            mediaRecorder.onstop = function() {
                clearTimeout(countdownTimer);
                timerDisplay.textContent = '';
                recIcon.style.display = 'none'; // Hide REC icon
            };

            mediaRecorder.start();
            console.log('Recording started');

            recIcon.style.display = 'block'; // Show REC icon

            // Start countdown timer
            let duration = 10; // 10 seconds
            countdown(duration);

            // Stop recording after the set duration
            setTimeout(() => {
                mediaRecorder.stop();
                console.log('Recording stopped');
            }, duration * 1000);
        }

        function countdown(seconds) {
            let counter = seconds;
            countdownTimer = setInterval(() => {
                if (counter >= 0) {
                    timerDisplay.textContent = 'Time left: ' + counter + ' seconds';
                    counter--;
                } else {
                    clearInterval(countdownTimer);
                }
            }, 1000);
        }

        function saveRecording() {
            if (recordedChunks.length) {
                let blob = new Blob(recordedChunks, { type: 'video/webm' });
                let url = URL.createObjectURL(blob);
                let a = document.createElement('a');
                a.style.display = 'none';
                a.href = url;
                a.download = 'recording.webm';
                document.body.appendChild(a);
                a.click();
                URL.revokeObjectURL(url);
                console.log('Recording saved to gallery');
            } else {
                console.log('No recording available to save');
            }
        }

        function shareCamera() {
            let code = generateCode();
            codeDisplay.textContent = 'Generated Code: ' + code;
            console.log('Generated Code:', code);
            // Add your camera sharing logic here
        }

        function generateCode() {
            let characters = 'ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789';
            let code = '';
            for (let i = 0; i < 8; i++) {
                code += characters.charAt(Math.floor(Math.random() * characters.length));
            }
            return code;
        }
    </script>
</body>
</html>
