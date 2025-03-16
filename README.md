<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>CBT Exam</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            background-color: #f4f4f4;
            text-align: center;
            padding: 20px;
        }
        .container {
            background: white;
            padding: 20px;
            border-radius: 10px;
            box-shadow: 0px 0px 10px rgba(0, 0, 0, 0.1);
            max-width: 600px;
            margin: auto;
        }
        .question-box {
            margin: 20px 0;
        }
        .timer {
            font-size: 20px;
            font-weight: bold;
            color: green;
        }
        .timer.warning {
            color: red;
        }
        .navigation {
            display: flex;
            justify-content: space-between;
            margin-top: 20px;
        }
        .submit-btn {
            margin-top: 20px;
            background: green;
            color: white;
            padding: 10px;
            border: none;
            cursor: pointer;
        }
    </style>
</head>
<body>
    <div class="container">
        <h2>CBT Exam</h2>
        <label for="difficulty">Select Difficulty: </label>
        <select id="difficulty">
            <option value="simple">Simple</option>
            <option value="medium" selected>Medium</option>
            <option value="difficult">Difficult</option>
            <option value="very_difficult">Very Difficult</option>
        </select>
        <br><br>
        <label for="timeLimit">Select Time Limit: </label>
        <select id="timeLimit">
            <script>
                for (let i = 5; i <= 150; i += 5) {
                    document.write(`<option value="${i}">${i} minutes</option>`);
                }
            </script>
        </select>
        <br><br>
        <input type="file" id="fileInput">
        <button onclick="uploadFile()">Upload File</button>
        <div class="timer" id="timer">05:00</div>
        <div class="question-box" id="question-box">
            <p id="question-text">Loading question...</p>
            <div id="options"></div>
        </div>
        <div class="navigation">
            <button onclick="prevQuestion()">Previous</button>
            <button onclick="nextQuestion()">Next</button>
        </div>
        <button class="submit-btn" onclick="submitExam()">Submit</button>
    </div>

    <script>
        let questions = [];
        let currentQuestionIndex = 0;
        let timer;

        function startTimer() {
            let selectedTime = document.getElementById("timeLimit").value;
            timer = selectedTime * 60;

            let interval = setInterval(() => {
                let minutes = Math.floor(timer / 60);
                let seconds = timer % 60;
                document.getElementById('timer').textContent = `${minutes.toString().padStart(2,'0')}:${seconds.toString().padStart(2,'0')}`;
                
                if (timer === 60) {
                    document.getElementById('timer').classList.add('warning');
                }
                if (timer === 0) {
                    clearInterval(interval);
                    submitExam();
                }
                timer--;
            }, 1000);
        }

        function uploadFile() {
            let fileInput = document.getElementById('fileInput').files[0];
            let difficulty = document.getElementById('difficulty').value;
            let formData = new FormData();
            formData.append("file", fileInput);
            formData.append("difficulty", difficulty);

            fetch("http://localhost:5000/generate_questions", {
                method: "POST",
                body: formData
            })
            .then(response => response.json())
            .then(data => {
                questions = data.questions.split("\n\n");
                loadQuestion(0);
                startTimer();
            })
            .catch(error => console.error("Error:", error));
        }

        function loadQuestion(index) {
            if (index < 0 || index >= questions.length) return;
            currentQuestionIndex = index;
            document.getElementById('question-text').textContent = questions[index];
        }

        function prevQuestion() {
            if (currentQuestionIndex > 0) {
                loadQuestion(currentQuestionIndex - 1);
            }
        }

        function nextQuestion() {
            if (currentQuestionIndex < questions.length - 1) {
                loadQuestion(currentQuestionIndex + 1);
            }
        }

        function submitExam() {
            alert("Exam submitted!");
        }
    </script>
</body>
</html>
