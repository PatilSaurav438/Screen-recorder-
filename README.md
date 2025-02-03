# Screen-recorder-
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Advanced Screen Recorder</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            text-align: center;
            background: linear-gradient(45deg, #ff9a9e, #fad0c4, #fad0c4, #fbc2eb);
            background-size: 400% 400%;
            animation: gradientBG 6s ease infinite;
            padding: 20px;
            color: #fff;
        }
        @keyframes gradientBG {
            0% { background-position: 0% 50%; }
            50% { background-position: 100% 50%; }
            100% { background-position: 0% 50%; }
        }
        h2 { margin-bottom: 20px; font-size: 28px; }
        button {
            padding: 12px 20px;
            font-size: 16px;
            border: none;
            border-radius: 25px;
            margin: 10px;
            cursor: pointer;
            transition: 0.3s;
        }
        button:hover { transform: scale(1.1); }
        #startBtn { background: #4CAF50; color: white; }
        #pauseBtn { background: #FF9800; color: white; }
        #resumeBtn { background: #009688; color: white; }
        #stopBtn { background: #F44336; color: white; }
        button:disabled { background: gray; cursor: not-allowed; }
        #timer {
            font-size: 22px;
            font-weight: bold;
            margin: 15px 0;
            background: rgba(0, 0, 0, 0.3);
            padding: 10px;
            border-radius: 10px;
            display: inline-block;
        }
        video {
            width: 80%;
            max-width: 600px;
            margin-top: 20px;
            border-radius: 10px;
            box-shadow: 0px 0px 10px rgba(0, 0, 0, 0.2);
        }
        #downloadLink {
            display: none;
            font-size: 18px;
            padding: 10px 15px;
            background: #00BCD4;
            color: white;
            text-decoration: none;
            border-radius: 20px;
            margin-top: 15px;
            display: inline-block;
        }
    </style>
</head>
<body>

    <h2>🎥 Advanced Screen Recorder</h2>

    <button id="startBtn">🎬 Start Recording</button>
    <button id="pauseBtn" disabled>⏸️ Pause</button>
    <button id="resumeBtn" disabled>▶️ Resume</button>
    <button id="stopBtn" disabled>🛑 Stop</button>
    
    <p id="timer">⏳ 00:00</p>
    
    <video id="preview" controls></video>
    
    <a id="downloadLink">⬇️ Download Recording</a>

    <script>
        let mediaRecorder;
        let recordedChunks = [];
        let stream;
        let timerInterval;
        let seconds = 0;

        function updateTimer() {
            seconds++;
            let mins = Math.floor(seconds / 60);
            let secs = seconds % 60;
            document.getElementById("timer").innerText = `⏳ ${(mins < 10 ? "0" + mins : mins)}:${(secs < 10 ? "0" + secs : secs)}`;
        }

        document.getElementById("startBtn").addEventListener("click", async () => {
            recordedChunks = [];
            seconds = 0;
            document.getElementById("timer").innerText = "⏳ 00:00";

            stream = await navigator.mediaDevices.getDisplayMedia({ video: true, audio: true });
            mediaRecorder = new MediaRecorder(stream, { mimeType: "video/webm" });

            mediaRecorder.ondataavailable = event => recordedChunks.push(event.data);
            mediaRecorder.onstop = () => {
                clearInterval(timerInterval);
                const blob = new Blob(recordedChunks, { type: "video/webm" });
                const url = URL.createObjectURL(blob);

                document.getElementById("preview").src = url;
                document.getElementById("downloadLink").href = url;
                document.getElementById("downloadLink").download = "screen-recording.webm";
                document.getElementById("downloadLink").style.display = "inline";
            };

            mediaRecorder.start();
            timerInterval = setInterval(updateTimer, 1000);

            document.getElementById("startBtn").disabled = true;
            document.getElementById("pauseBtn").disabled = false;
            document.getElementById("stopBtn").disabled = false;
        });

        document.getElementById("pauseBtn").addEventListener("click", () => {
            mediaRecorder.pause();
            clearInterval(timerInterval);
            document.getElementById("pauseBtn").disabled = true;
            document.getElementById("resumeBtn").disabled = false;
        });

        document.getElementById("resumeBtn").addEventListener("click", () => {
            mediaRecorder.resume();
            timerInterval = setInterval(updateTimer, 1000);
            document.getElementById("pauseBtn").disabled = false;
            document.getElementById("resumeBtn").disabled = true;
        });

        document.getElementById("stopBtn").addEventListener("click", () => {
            mediaRecorder.stop();
            stream.getTracks().forEach(track => track.stop());
            clearInterval(timerInterval);

            document.getElementById("startBtn").disabled = false;
            document.getElementById("pauseBtn").disabled = true;
            document.getElementById("resumeBtn").disabled = true;
            document.getElementById("stopBtn").disabled = true;
        });

    </script>

</body>
</html>
