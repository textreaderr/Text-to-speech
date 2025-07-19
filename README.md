# Text-to-speech
A program that takes texts and reads it to you
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Human-like Text-to-Speech</title>
    <style>
        body {
            font-family: 'Segoe UI', Roboto, sans-serif;
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            min-height: 100vh;
            margin: 0;
            padding: 20px;
            background-color: #f5f7fa;
            color: #2d3748;
            line-height: 1.6;
        }
        h1 {
            font-size: 2.5em;
            margin-bottom: 20px;
            text-align: center;
            color: #1a365d;
            font-weight: 600;
        }
        #text-input {
            width: 80%;
            max-width: 700px;
            height: 150px;
            padding: 15px;
            font-size: 1.2em;
            border: 2px solid #4299e1;
            border-radius: 10px;
            resize: none;
            margin-bottom: 20px;
            box-shadow: 0 2px 4px rgba(0,0,0,0.1);
            transition: border-color 0.3s;
        }
        #text-input:focus {
            border-color: #3182ce;
            outline: none;
            box-shadow: 0 0 0 3px rgba(66, 153, 225, 0.5);
        }
        .controls {
            width: 80%;
            max-width: 700px;
            margin-bottom: 20px;
            display: flex;
            flex-direction: column;
            gap: 15px;
            background: white;
            padding: 20px;
            border-radius: 10px;
            box-shadow: 0 4px 6px rgba(0,0,0,0.1);
        }
        .control-group {
            display: flex;
            flex-direction: column;
            gap: 8px;
        }
        label {
            font-weight: 600;
            font-size: 1em;
            color: #4a5568;
        }
        select {
            width: 100%;
            padding: 10px;
            border-radius: 6px;
            border: 1px solid #cbd5e0;
            font-size: 1em;
            background-color: #f7fafc;
            transition: all 0.3s;
        }
        select:focus {
            border-color: #4299e1;
            outline: none;
            box-shadow: 0 0 0 3px rgba(66, 153, 225, 0.5);
        }
        .slider-container {
            display: flex;
            flex-direction: column;
            gap: 5px;
        }
        .slider-labels {
            display: flex;
            justify-content: space-between;
            font-size: 0.85em;
            color: #718096;
        }
        input[type="range"] {
            width: 100%;
            height: 8px;
            -webkit-appearance: none;
            background: #e2e8f0;
            border-radius: 4px;
            outline: none;
        }
        input[type="range"]::-webkit-slider-thumb {
            -webkit-appearance: none;
            width: 20px;
            height: 20px;
            background: #4299e1;
            border-radius: 50%;
            cursor: pointer;
            transition: all 0.2s;
        }
        input[type="range"]::-webkit-slider-thumb:hover {
            background: #3182ce;
            transform: scale(1.1);
        }
        .button-group {
            display: flex;
            gap: 15px;
            margin-top: 15px;
        }
        button {
            padding: 12px 24px;
            font-size: 1.1em;
            border: none;
            border-radius: 8px;
            cursor: pointer;
            transition: all 0.3s;
            flex: 1;
            font-weight: 600;
            display: flex;
            align-items: center;
            justify-content: center;
            gap: 8px;
        }
        #speak-button {
            background-color: #4299e1;
            color: white;
        }
        #speak-button:hover {
            background-color: #3182ce;
        }
        #speak-button.speaking {
            background-color: #e53e3e;
        }
        #stop-button {
            background-color: #e53e3e;
            color: white;
            display: none;
        }
        #stop-button:hover {
            background-color: #c53030;
        }
        #status {
            margin-top: 15px;
            font-size: 1.1em;
            color: #4a5568;
            text-align: center;
            max-width: 80%;
            min-height: 24px;
            padding: 10px;
            border-radius: 6px;
            background-color: white;
            box-shadow: 0 2px 4px rgba(0,0,0,0.05);
        }
        .voice-preview {
            margin-top: 5px;
            font-size: 0.85em;
            color: #718096;
            font-style: italic;
        }
        .quality-indicator {
            display: inline-block;
            padding: 2px 6px;
            border-radius: 4px;
            font-size: 0.75em;
            font-weight: bold;
            margin-left: 8px;
        }
        .high-quality {
            background-color: #48bb78;
            color: white;
        }
        .medium-quality {
            background-color: #ed8936;
            color: white;
        }
        .low-quality {
            background-color: #e53e3e;
            color: white;
        }
        @media (max-width: 768px) {
            h1 {
                font-size: 2em;
            }
            #text-input, .controls {
                width: 90%;
            }
            button {
                padding: 10px 15px;
                font-size: 1em;
            }
        }
    </style>
</head>
<body>
    <h1>Human-like Text-to-Speech</h1>
    <textarea id="text-input" placeholder="Enter the text you want to hear spoken in a natural human voice..."></textarea>
    
    <div class="controls">
        <div class="control-group">
            <label for="voice-select">Select Voice:</label>
            <select id="voice-select"></select>
            <div class="voice-preview">Select a voice to hear a preview</div>
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
        
        <div class="button-group">
            <button id="speak-button">
                <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" fill="currentColor" viewBox="0 0 16 16">
                    <path d="M11.536 14.01A8.473 8.473 0 0 0 14.026 8a8.473 8.473 0 0 0-2.49-6.01l-.708.707A7.476 7.476 0 0 1 13.025 8c0 2.071-.84 3.946-2.197 5.303l.708.707z"/>
                    <path d="M10.121 12.596A6.48 6.48 0 0 0 12.025 8a6.48 6.48 0 0 0-1.904-4.596l-.707.707A5.483 5.483 0 0 1 11.025 8a5.483 5.483 0 0 1-1.61 3.89l.706.706z"/>
                    <path d="M8.707 11.182A4.486 4.486 0 0 0 10.025 8c0-1.243-.53-2.369-1.377-3.182l-.707.707A3.489 3.489 0 0 1 9.025 8c0 .966-.392 1.841-1.024 2.475l.706.707zM6.717 3.55A.5.5 0 0 1 7 4v8a.5.5 0 0 1-1 0V4a.5.5 0 0 1 .717-.454l3 1.5a.5.5 0 0 1 0 .908l-3 1.5z"/>
                </svg>
                Speak Text
            </button>
            <button id="stop-button">
                <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" fill="currentColor" viewBox="0 0 16 16">
                    <path d="M5 3.5h6A1.5 1.5 0 0 1 12.5 5v6a1.5 1.5 0 0 1-1.5 1.5H5A1.5 1.5 0 0 1 3.5 11V5A1.5 1.5 0 0 1 5 3.5z"/>
                </svg>
                Stop
            </button>
        </div>
    </div>
    
    <div id="status">Enter text and select a voice to begin</div>

    <script>
        const speakButton = document.getElementById('speak-button');
        const stopButton = document.getElementById('stop-button');
        const textInput = document.getElementById('text-input');
        const voiceSelect = document.getElementById('voice-select');
        const rateControl = document.getElementById('rate-control');
        const pitchControl = document.getElementById('pitch-control');
        const status = document.getElementById('status');
        const voicePreview = document.querySelector('.voice-preview');

        // Check if Web Speech API is available
        if (!window.speechSynthesis) {
            status.textContent = 'Error: Your browser does not support text-to-speech. Please use Chrome, Edge, or Safari for the best experience.';
            speakButton.disabled = true;
            stopButton.disabled = true;
        }

        let voices = [];
        let isSpeaking = false;
        let currentUtterance = null;

        // Function to rate voice quality (higher is better)
        function rateVoiceQuality(voice) {
            // Prioritize voices that are likely to be more natural
            let score = 0;
            
            // Higher scores for neural/advanced voices
            if (voice.name.includes('Neural') || voice.name.includes('Natural')) score += 3;
            if (voice.name.includes('WaveNet')) score += 4;
            
            // Prefer certain providers known for better quality
            if (voice.voiceURI.includes('Amazon') || voice.voiceURI.includes('Polly')) score += 2;
            if (voice.voiceURI.includes('Microsoft') || voice.voiceURI.includes('Zira')) score += 1;
            if (voice.voiceURI.includes('Google') || voice.voiceURI.includes('Wavenet')) score += 2;
            
            // Prefer certain languages/locales
            if (voice.lang.includes('en-US') || voice.lang.includes('en-GB')) score += 1;
            
            // Prefer default voices
            if (voice.default) score += 1;
            
            return score;
        }

        // Populate voice options with quality indicators
        function populateVoiceList() {
            voices = window.speechSynthesis.getVoices();
            voiceSelect.innerHTML = '';
            
            if (voices.length === 0) {
                status.textContent = 'Loading voices... please wait.';
                setTimeout(populateVoiceList, 200);
                return;
            }

            // Sort voices by quality (highest first)
            voices.sort((a, b) => rateVoiceQuality(b) - rateVoiceQuality(a));

            // Create voice options
            voices.forEach(voice => {
                const option = document.createElement('option');
                const qualityScore = rateVoiceQuality(voice);
                
                let qualityClass = 'low-quality';
                let qualityText = 'Basic';
                if (qualityScore >= 3) {
                    qualityClass = 'medium-quality';
                    qualityText = 'Good';
                }
                if (qualityScore >= 5) {
                    qualityClass = 'high-quality';
                    qualityText = 'Premium';
                }
                
                option.textContent = `${voice.name} (${voice.lang})`;
                option.setAttribute('data-name', voice.name);
                option.setAttribute('data-lang', voice.lang);
                option.innerHTML += ` <span class="quality-indicator ${qualityClass}">${qualityText}</span>`;
                voiceSelect.appendChild(option);
            });

            // Select the highest quality voice by default
            if (voices.length > 0) {
                const defaultVoice = voices[0];
                voiceSelect.selectedIndex = 0;
                status.textContent = `Loaded ${voices.length} voices. Highest quality voice selected: ${defaultVoice.name}`;
                
                // Preview the default voice
                previewVoice(defaultVoice);
            }

            speakButton.disabled = false;
        }

        // Preview a voice
        function previewVoice(voice) {
            const previewText = "Hello, this is a preview of how I sound. Listen to my natural speech patterns.";
            const utterance = new SpeechSynthesisUtterance(previewText);
            
            // Optimize for natural speech
            utterance.voice = voice;
            utterance.rate = 1.0;
            utterance.pitch = 1.0;
            utterance.volume = 1;
            
            // Cancel any current speech
            window.speechSynthesis.cancel();
            
            // Update preview text
            voicePreview.textContent = `Preview: ${voice.name} (${voice.lang})`;
            
            // Speak the preview
            window.speechSynthesis.speak(utterance);
        }

        // Handle voice selection changes
        voiceSelect.addEventListener('change', () => {
            const selectedOption = voiceSelect.selectedOptions[0];
            const selectedVoiceName = selectedOption.getAttribute('data-name');
            const selectedVoice = voices.find(voice => voice.name === selectedVoiceName);
            
            if (selectedVoice) {
                previewVoice(selectedVoice);
            }
        });

        // Initialize voice loading
        if (window.speechSynthesis) {
            speechSynthesis.onvoiceschanged = populateVoiceList;
            
            // Load voices immediately if available
            if (speechSynthesis.getVoices().length > 0) {
                populateVoiceList();
            } else {
                // Some browsers need a kick to load voices
                const kick = new SpeechSynthesisUtterance('');
                kick.volume = 0;
                speechSynthesis.speak(kick);
                setTimeout(populateVoiceList, 100);
            }
        }

        // Handle speak button click
        speakButton.addEventListener('click', () => {
            const text = textInput.value.trim();

            if (!text) {
                status.textContent = 'Please enter some text to speak.';
                return;
            }

            if (voices.length === 0) {
                status.textContent = 'Error: Voices not loaded yet. Please wait or refresh the page.';
                return;
            }

            // Get selected voice
            const selectedOption = voiceSelect.selectedOptions[0];
            const selectedVoiceName = selectedOption.getAttribute('data-name');
            const selectedVoice = voices.find(voice => voice.name === selectedVoiceName);

            // Create utterance with optimized settings for natural speech
            currentUtterance = new SpeechSynthesisUtterance(text);
            currentUtterance.voice = selectedVoice;
            
            // Fine-tuned parameters for natural speech
            currentUtterance.rate = parseFloat(rateControl.value);
            currentUtterance.pitch = parseFloat(pitchControl.value);
            currentUtterance.volume = 1;
            
            // Add slight pauses for punctuation (makes speech more natural)
            currentUtterance.text = text.replace(/([,.!?;:])/g, '$1 ');

            // Update UI
            speakButton.classList.add('speaking');
            speakButton.textContent = 'Speaking...';
            stopButton.style.display = 'block';
            status.textContent = `Speaking with ${selectedVoice.name}...`;
            isSpeaking = true;

            // Speech event handlers
            currentUtterance.onboundary = (event) => {
                // You could highlight words as they're spoken here
            };

            currentUtterance.onend = () => {
                speakButton.classList.remove('speaking');
                speakButton.innerHTML = '<svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" fill="currentColor" viewBox="0 0 16 16"><path d="M11.536 14.01A8.473 8.473 0 0 0 14.026 8a8.473 8.473 0 0 0-2.49-6.01l-.708.707A7.476 7.476 0 0 1 13.025 8c0 2.071-.84 3.946-2.197 5.303l.708.707z"/><path d="M10.121 12.596A6.48 6.48 0 0 0 12.025 8a6.48 6.48 0 0 0-1.904-4.596l-.707.707A5.483 5.483 0 0 1 11.025 8a5.483 5.483 0 0 1-1.61 3.89l.706.706z"/><path d="M8.707 11.182A4.486 4.486 0 0 0 10.025 8c0-1.243-.53-2.369-1.377-3.182l-.707.707A3.489 3.489 0 0 1 9.025 8c0 .966-.392 1.841-1.024 2.475l.706.707zM6.717 3.55A.5.5 0 0 1 7 4v8a.5.5 0 0 1-1 0V4a.5.5 0 0 1 .717-.454l3 1.5a.5.5 0 0 1 0 .908l-3 1.5z"/></svg> Speak Text';
                stopButton.style.display = 'none';
                status.textContent = 'Finished speaking.';
                isSpeaking = false;
                currentUtterance = null;
            };

            currentUtterance.onerror = (event) => {
                status.textContent = `Error: ${event.error}. Try a different voice or check your audio settings.`;
                speakButton.classList.remove('speaking');
                speakButton.innerHTML = '<svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" fill="currentColor" viewBox="0 0 16 16"><path d="M11.536 14.01A8.473 8.473 0 0 0 14.026 8a8.473 8.473 0 0 0-2.49-6.01l-.708.707A7.476 7.476 0 0 1 13.025 8c0 2.071-.84 3.946-2.197 5.303l.708.707z"/><path d="M10.121 12.596A6.48 6.48 0 0 0 12.025 8a6.48 6.48 0 0 0-1.904-4.596l-.707.707A5.483 5.483 0 0 1 11.025 8a5.483 5.483 0 0 1-1.61 3.89l.706.706z"/><path d="M8.707 11.182A4.486 4.486 0 0 0 10.025 8c0-1.243-.53-2.369-1.377-3.182l-.707.707A3.489 3.489 0 0 1 9.025 8c0 .966-.392 1.841-1.024 2.475l.706.707zM6.717 3.55A.5.5 0 0 1 7 4v8a.5.5 0 0 1-1 0V4a.5.5 0 0 1 .717-.454l3 1.5a.5.5 0 0 1 0 .908l-3 1.5z"/></svg> Speak Text';
                stopButton.style.display = 'none';
                isSpeaking = false;
                currentUtterance = null;
            };

            // Start speaking
            window.speechSynthesis.speak(currentUtterance);
        });

        // Handle stop button click
        stopButton.addEventListener('click', () => {
            window.speechSynthesis.cancel();
            speakButton.classList.remove('speaking');
            speakButton.innerHTML = '<svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" fill="currentColor" viewBox="0 0 16 16"><path d="M11.536 14.01A8.473 8.473 0 0 0 14.026 8a8.473 8.473 0 0 0-2.49-6.01l-.708.707A7.476 7.476 0 0 1 13.025 8c0 2.071-.84 3.946-2.197 5.303l.708.707z"/><path d="M10.121 12.596A6.48 6.48 0 0 0 12.025 8a6.48 6.48 0 0 0-1.904-4.596l-.707.707A5.483 5.483 0 0 1 11.025 8a5.483 5.483 0 0 1-1.61 3.89l.706.706z"/><path d="M8.707 11.182A4.486 4.486 0 0 0 10.025 8c0-1.243-.53-2.369-1.377-3.182l-.707.707A3.489 3.489 0 0 1 9.025 8c0 .966-.392 1.841-1.024 2.475l.706.707zM6.717 3.55A.5.5 0 0 1 7 4v8a.5.5 0 0 1-1 0V4a.5.5 0 0 1 .717-.454l3 1.5a.5.5 0 0 1 0 .908l-3 1.5z"/></svg> Speak Text';
            stopButton.style.display = 'none';
            status.textContent = 'Speech stopped.';
            isSpeaking = false;
            currentUtterance = null;
        });

        // Update status when user types
        textInput.addEventListener('input', () => {
            if (textInput.value.trim()) {
                status.textContent = 'Ready to speak. Select a voice and click Speak.';
            } else {
                status.textContent = 'Enter text to speak.';
            }
        });

        // Optimize for natural speech parameters
        rateControl.value = 1.0; // Natural speaking rate
        pitchControl.value = 1.0; // Neutral pitch
    </script>
</body>
</html>
