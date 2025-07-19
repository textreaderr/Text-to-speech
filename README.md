# Text-to-speech
A program that takes texts and reads it to you
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Premium Human-like Text-to-Speech</title>
    <style>
        :root {
            --primary: #2563eb;
            --primary-dark: #1d4ed8;
            --secondary: #dc2626;
            --secondary-dark: #b91c1c;
            --text: #1f2937;
            --text-light: #6b7280;
            --bg: #f9fafb;
            --card: #ffffff;
            --shadow: 0 4px 6px -1px rgba(0, 0, 0, 0.1);
        }
        
        body {
            font-family: 'Inter', -apple-system, BlinkMacSystemFont, sans-serif;
            line-height: 1.6;
            color: var(--text);
            background-color: var(--bg);
            margin: 0;
            padding: 2rem;
            min-height: 100vh;
            display: flex;
            flex-direction: column;
            align-items: center;
        }
        
        .container {
            width: 100%;
            max-width: 800px;
            margin: 0 auto;
        }
        
        h1 {
            font-size: 2.5rem;
            font-weight: 700;
            color: var(--text);
            margin-bottom: 1.5rem;
            text-align: center;
            background: linear-gradient(90deg, #2563eb, #4f46e5);
            -webkit-background-clip: text;
            background-clip: text;
            color: transparent;
        }
        
        .card {
            background-color: var(--card);
            border-radius: 12px;
            padding: 2rem;
            box-shadow: var(--shadow);
            margin-bottom: 2rem;
        }
        
        textarea {
            width: 100%;
            min-height: 150px;
            padding: 1rem;
            border: 2px solid #e5e7eb;
            border-radius: 8px;
            font-size: 1rem;
            resize: vertical;
            transition: border-color 0.2s;
        }
        
        textarea:focus {
            outline: none;
            border-color: var(--primary);
            box-shadow: 0 0 0 3px rgba(37, 99, 235, 0.1);
        }
        
        .controls {
            display: grid;
            grid-template-columns: 1fr;
            gap: 1.5rem;
            margin-top: 1.5rem;
        }
        
        .control-group {
            display: flex;
            flex-direction: column;
            gap: 0.5rem;
        }
        
        label {
            font-weight: 600;
            font-size: 0.875rem;
            color: var(--text);
        }
        
        select {
            padding: 0.75rem;
            border-radius: 8px;
            border: 1px solid #e5e7eb;
            font-size: 1rem;
            background-color: white;
        }
        
        select:focus {
            outline: none;
            border-color: var(--primary);
            box-shadow: 0 0 0 3px rgba(37, 99, 235, 0.1);
        }
        
        .slider-container {
            display: flex;
            flex-direction: column;
            gap: 0.5rem;
        }
        
        .slider-labels {
            display: flex;
            justify-content: space-between;
            font-size: 0.75rem;
            color: var(--text-light);
        }
        
        input[type="range"] {
            -webkit-appearance: none;
            width: 100%;
            height: 8px;
            background: #e5e7eb;
            border-radius: 4px;
            outline: none;
        }
        
        input[type="range"]::-webkit-slider-thumb {
            -webkit-appearance: none;
            appearance: none;
            width: 20px;
            height: 20px;
            background: var(--primary);
            border-radius: 50%;
            cursor: pointer;
            transition: all 0.2s;
        }
        
        input[type="range"]::-webkit-slider-thumb:hover {
            transform: scale(1.1);
            background: var(--primary-dark);
        }
        
        .button-group {
            display: flex;
            gap: 1rem;
            margin-top: 1.5rem;
        }
        
        button {
            display: inline-flex;
            align-items: center;
            justify-content: center;
            gap: 0.5rem;
            padding: 0.75rem 1.5rem;
            border-radius: 8px;
            font-weight: 600;
            font-size: 1rem;
            cursor: pointer;
            transition: all 0.2s;
            border: none;
        }
        
        #speak-button {
            background-color: var(--primary);
            color: white;
            flex: 1;
        }
        
        #speak-button:hover {
            background-color: var(--primary-dark);
        }
        
        #pause-button {
            background-color: #f59e0b;
            color: white;
            display: none;
        }
        
        #pause-button:hover {
            background-color: #d97706;
        }
        
        #stop-button {
            background-color: var(--secondary);
            color: white;
        }
        
        #stop-button:hover {
            background-color: var(--secondary-dark);
        }
        
        .status {
            margin-top: 1rem;
            font-size: 0.875rem;
            color: var(--text-light);
            text-align: center;
        }
        
        .progress-container {
            width: 100%;
            height: 6px;
            background-color: #e5e7eb;
            border-radius: 3px;
            margin-top: 1rem;
            overflow: hidden;
            display: none;
        }
        
        .progress-bar {
            height: 100%;
            background-color: var(--primary);
            width: 0%;
            transition: width 0.1s linear;
        }
        
        .highlight {
            background-color: rgba(37, 99, 235, 0.2);
            transition: background-color 0.2s;
        }
        
        @media (max-width: 768px) {
            body {
                padding: 1rem;
            }
            
            h1 {
                font-size: 2rem;
            }
            
            .card {
                padding: 1.5rem;
            }
            
            .button-group {
                flex-direction: column;
            }
        }
    </style>
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700&display=swap" rel="stylesheet">
</head>
<body>
    <div class="container">
        <h1>Premium Human-like Text-to-Speech</h1>
        
        <div class="card">
            <textarea id="text-input" placeholder="Enter the text you want to convert to ultra-realistic speech..."></textarea>
            
            <div class="controls">
                <div class="control-group">
                    <label for="voice-select">Select Premium Voice:</label>
                    <select id="voice-select">
                        <option value="Joanna">Joanna (Neural) - Female</option>
                        <option value="Matthew">Matthew (Neural) - Male</option>
                        <option value="Kendra">Kendra (Neural) - Female</option>
                        <option value="Justin">Justin (Neural) - Male</option>
                        <option value="Kimberly">Kimberly (Neural) - Female</option>
                    </select>
                </div>
                
                <div class="control-group">
                    <label for="rate-control">Speech Rate:</label>
                    <div class="slider-container">
                        <input type="range" id="rate-control" min="0.7" max="1.3" step="0.05" value="1.0">
                        <div class="slider-labels">
                            <span>Slower</span>
                            <span>Natural</span>
                            <span>Faster</span>
                        </div>
                    </div>
                </div>
                
                <div class="control-group">
                    <label for="pitch-control">Voice Pitch:</label>
                    <div class="slider-container">
                        <input type="range" id="pitch-control" min="0.8" max="1.2" step="0.05" value="1.0">
                        <div class="slider-labels">
                            <span>Deeper</span>
                            <span>Natural</span>
                            <span>Higher</span>
                        </div>
                    </div>
                </div>
            </div>
            
            <div class="progress-container" id="progress-container">
                <div class="progress-bar" id="progress-bar"></div>
            </div>
            
            <div class="button-group">
                <button id="speak-button">
                    <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" fill="currentColor" viewBox="0 0 16 16">
                        <path d="M11.536 14.01A8.473 8.473 0 0 0 14.026 8a8.473 8.473 0 0 0-2.49-6.01l-.708.707A7.476 7.476 0 0 1 13.025 8c0 2.071-.84 3.946-2.197 5.303l.708.707z"/>
                        <path d="M10.121 12.596A6.48 6.48 0 0 0 12.025 8a6.48 6.48 0 0 0-1.904-4.596l-.707.707A5.483 5.483 0 0 1 11.025 8a5.483 5.483 0 0 1-1.61 3.89l.706.706z"/>
                        <path d="M8.707 11.182A4.486 4.486 0 0 0 10.025 8c0-1.243-.53-2.369-1.377-3.182l-.707.707A3.489 3.489 0 0 1 9.025 8c0 .966-.392 1.841-1.024 2.475l.706.707zM6.717 3.55A.5.5 0 0 1 7 4v8a.5.5 0 0 1-1 0V4a.5.5 0 0 1 .717-.454l3 1.5a.5.5 0 0 1 0 .908l-3 1.5z"/>
                    </svg>
                    Speak
                </button>
                <button id="pause-button">
                    <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" fill="currentColor" viewBox="0 0 16 16">
                        <path d="M5.5 3.5A1.5 1.5 0 0 1 7 5v6a1.5 1.5 0 0 1-3 0V5a1.5 1.5 0 0 1 1.5-1.5zm5 0A1.5 1.5 0 0 1 12 5v6a1.5 1.5 0 0 1-3 0V5a1.5 1.5 0 0 1 1.5-1.5z"/>
                    </svg>
                    Pause
                </button>
                <button id="stop-button">
                    <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" fill="currentColor" viewBox="0 0 16 16">
                        <path d="M5 3.5h6A1.5 1.5 0 0 1 12.5 5v6a1.5 1.5 0 0 1-1.5 1.5H5A1.5 1.5 0 0 1 3.5 11V5A1.5 1.5 0 0 1 5 3.5z"/>
                    </svg>
                    Stop
                </button>
            </div>
            
            <div class="status" id="status">Ready to convert text to ultra-realistic speech</div>
        </div>
    </div>

    <!-- Include AWS SDK for Polly (Neural TTS) -->
    <script src="https://sdk.amazonaws.com/js/aws-sdk-2.1040.0.min.js"></script>
    
    <script>
        // DOM Elements
        const textInput = document.getElementById('text-input');
        const voiceSelect = document.getElementById('voice-select');
        const rateControl = document.getElementById('rate-control');
        const pitchControl = document.getElementById('pitch-control');
        const speakButton = document.getElementById('speak-button');
        const pauseButton = document.getElementById('pause-button');
        const stopButton = document.getElementById('stop-button');
        const status = document.getElementById('status');
        const progressContainer = document.getElementById('progress-container');
        const progressBar = document.getElementById('progress-bar');
        
        // AWS Polly Configuration
        const awsConfig = {
            region: 'us-east-1',
            accessKeyId: 'AKIAYOURACCESSKEY', // Replace with your AWS access key
            secretAccessKey: 'YOURSECRETACCESSKEY', // Replace with your AWS secret key
        };
        
        // Initialize AWS Polly
        AWS.config.update(awsConfig);
        const polly = new AWS.Polly({
            apiVersion: '2016-06-10',
            signatureVersion: 'v4'
        });
        
        // Speech state variables
        let audioContext;
        let audioBuffer;
        let audioSource;
        let isPlaying = false;
        let isPaused = false;
        let pauseTime = 0;
        let startTime = 0;
        let currentWordIndex = 0;
        let wordTimings = [];
        let speechMarkers = [];
        
        // Highlight current word being spoken
        function highlightWord(index) {
            // Remove previous highlights
            const words = textInput.value.split(/\s+/);
            const text = textInput.value;
            
            // Create HTML with highlighted word
            let html = '';
            let currentPos = 0;
            
            words.forEach((word, i) => {
                const wordPos = text.indexOf(word, currentPos);
                const before = text.substring(currentPos, wordPos);
                const after = text.substring(wordPos + word.length);
                
                if (i === index) {
                    html += `${before}<span class="highlight">${word}</span>`;
                } else {
                    html += `${before}${word}`;
                }
                
                currentPos = wordPos + word.length;
            });
            
            textInput.innerHTML = html;
        }
        
        // Speak text using AWS Polly (Neural TTS)
        async function speakText() {
            const text = textInput.value.trim();
            
            if (!text) {
                status.textContent = 'Please enter text to speak';
                return;
            }
            
            // Show progress bar
            progressContainer.style.display = 'block';
            progressBar.style.width = '0%';
            
            // Disable buttons during processing
            speakButton.disabled = true;
            status.textContent = 'Generating ultra-realistic speech...';
            
            try {
                // Get selected voice and parameters
                const voiceId = voiceSelect.value;
                const rate = rateControl.value;
                
                // Request speech marks for word-level tracking
                const speechMarksParams = {
                    OutputFormat: 'json',
                    Text: text,
                    VoiceId: voiceId,
                    SpeechMarkTypes: ['word']
                };
                
                // Request audio with neural engine
                const audioParams = {
                    OutputFormat: 'mp3',
                    Text: text,
                    VoiceId: voiceId,
                    Engine: 'neural',
                    TextType: 'text',
                    SampleRate: '24000'
                };
                
                // Get speech marks first
                const speechMarksResponse = await polly.synthesizeSpeech(speechMarksParams).promise();
                const speechMarks = speechMarksResponse.AudioStream.toString()
                    .split('\n')
                    .filter(mark => mark)
                    .map(mark => JSON.parse(mark));
                
                // Then get audio
                const audioResponse = await polly.synthesizeSpeech(audioParams).promise();
                const audioArrayBuffer = await audioResponse.AudioStream.transformToByteArray();
                
                // Set up audio context
                if (!audioContext) {
                    audioContext = new (window.AudioContext || window.webkitAudioContext)();
                }
                
                // Decode audio data
                audioBuffer = await audioContext.decodeAudioData(audioArrayBuffer.buffer);
                
                // Prepare for playback
                speechMarkers = speechMarks;
                currentWordIndex = 0;
                isPlaying = true;
                isPaused = false;
                
                // Update UI
                speakButton.disabled = false;
                pauseButton.style.display = 'inline-flex';
                status
