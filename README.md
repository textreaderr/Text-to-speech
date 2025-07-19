# Text-to-speech
A program that takes texts and reads it to you
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Text-to-Speech Interface (Chris Hansen Voice)</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            min-height: 100vh;
            margin: 0;
            padding: 20px;
            background-color: #f0f0f0;
            color: #333;
        }
        h1 {
            font-size: 2.5em;
            margin-bottom: 20px;
            text-align: center;
        }
        #text-input {
            width: 80%;
            max-width: 600px;
            height: 150px;
            padding: 10px;
            font-size: 1.2em;
            border: 2px solid #007bff;
            border-radius: 8px;
            resize: none;
            margin-bottom: 15px;
        }
        .controls {
            width: 80%;
            max-width: 600px;
            margin-bottom: 15px;
            display: flex;
            flex-direction: column;
            gap: 10px;
        }
        .control-group {
            display: flex;
            flex-direction: column;
            gap: 5px;
        }
        label {
            font-weight: bold;
            font-size: 0.9em;
        }
        select, input[type="range"] {
            width: 100%;
            padding: 8px;
            border-radius: 4px;
            border: 1px solid #ccc;
        }
        .button-group {
            display: flex;
            gap: 10px;
            margin-top: 10px;
        }
        button {
            padding: 10px 20px;
            font-size: 1.1em;
            border: none;
            border-radius: 8px;
            cursor: pointer;
            transition: background-color 0.3s;
            flex: 1;
        }
        #speak-button {
            background-color: #007bff;
            color: white;
        }
        #speak-button:hover {
            background-color: #0056b3;
        }
        #speak-button.speaking {
            background-color: #dc3545;
        }
        #stop-button {
            background-color: #dc3545;
            color: white;
            display: none;
        }
        #stop-button:hover {
            background-color: #bb2d3b;
        }
        #status {
            margin-top: 10px;
            font-size: 1.1em;
            color: #555;
            text-align: center;
            max-width: 80%;
            min-height: 20px;
        }
        .range-value {
            display: flex;
            justify-content: space-between;
            font-size: 0.8em;
            color: #666;
        }
        @media (max-width: 600px) {
            h1 {
                font-size: 1.8em;
            }
            #text-input {
                width: 90%;
                height: 100px;
            }
            .controls {
                width: 90%;
            }
            button {
                font-size: 1em;
                padding: 8px 16px;
            }
        }
    </style>
</head>
<body>
    <h1>Text-to-Speech Interface</h1>
    <textarea id="text-input" placeholder="Type or paste text to speak..."></textarea>
    
    <div class="controls">
        <div class="control-group">
            <label for="voice-select">Select Voice:</label>
            <select id="voice-select"></select>
        </div>
        
        <div class="control-group">
            <label for="rate-control">Speech Rate:</label>
            <input type="range" id="rate-control" min="0.5" max="2" step="0.1" value="1.0">
            <div class="range-value">
                <span>Slow</span>
                <span id="rate-value">1.0</span>
                <span>Fast</span>
            </div>
        </div>
        
        <div class="control-group">
            <label for="pitch-control">Voice Pitch:</label>
            <input type="range" id="pitch-control" min="0.5" max="2" step="0.1" value="0.8">
            <div class="range-value">
                <span>Low</span>
                <span id="pitch-value">0.8</span>
                <span>High</span>
            </div>
        </div>
        
        <div class="button-group">
            <button id="speak-button">Speak Text</button>
            <button id="stop-button">Stop</button>
        </div>
    </div>
    
    <div id="status">Enter text and click the button to hear it spoken</div>

    <script>
        const speakButton = document.getElementById('speak-button');
        const stopButton = document.getElementById('stop-button');
        const textInput = document.getElementById('text-input');
        const voiceSelect = document.getElementById('voice-select');
        const rateControl = document.getElementById('rate-control');
        const pitchControl = document.getElementById('pitch-control');
        const rateValue = document.getElementById('rate-value');
        const pitchValue = document.getElementById('pitch-value');
        const status = document.getElementById('status');

        // Update displayed values for sliders
        rateControl.addEventListener('input', () => {
            rateValue.textContent = rateControl.value;
        });
        
        pitchControl.addEventListener('input', () => {
            pitchValue.textContent = pitchControl.value;
        });

        // Check if Web Speech API is available
        if (!window.speechSynthesis) {
            status.textContent = 'Error: Browser does not support text-to-speech. Use Chrome or Safari.';
            speakButton.disabled = true;
            stopButton.disabled = true;
        }

        let isSpeaking = false;
        let voices = [];
        let defaultVoice = null;

        // Populate voice options
        function populateVoiceList() {
            voices = window.speechSynthesis.getVoices();
            voiceSelect.innerHTML = '';
            
            if (voices.length === 0) {
                status.textContent = 'Error: No voices loaded. Try Chrome or Safari, or check device settings.';
                speakButton.disabled = true;
                stopButton.disabled = true;
                return;
            }

            // Create voice options
            voices.forEach(voice => {
                const option = document.createElement('option');
                option.textContent = `${voice.name} (${voice.lang})${voice.default ? ' — DEFAULT' : ''}`;
                option.setAttribute('data-name', voice.name);
                option.setAttribute('data-lang', voice.lang);
                voiceSelect.appendChild(option);
            });

            // Select a male, American English voice by default if available
            defaultVoice = voices.find(voice => 
                voice.lang.includes('en-US') && 
                (voice.name.toLowerCase().includes('male') || 
                 voice.name.includes('David') || 
                 voice.name.includes('Mark'))
            ) || voices.find(voice => voice.lang.includes('en-US')) || voices[0];

            // Set the selected voice in the dropdown
            if (defaultVoice) {
                const options = voiceSelect.options;
                for (let i = 0; i < options.length; i++) {
                    if (options[i].getAttribute('data-name') === defaultVoice.name) {
                        voiceSelect.selectedIndex = i;
                        break;
                    }
                }
            }

            status.textContent = 'Voices loaded. Enter text to speak.';
            speakButton.disabled = false;
            stopButton.disabled = false;
        }

        // Handle voice loading
        if (window.speechSynthesis) {
            // Chrome loads voices asynchronously
            window.speechSynthesis.onvoiceschanged = populateVoiceList;
            
            // Safari loads voices immediately
            if (speechSynthesis.getVoices().length > 0) {
                populateVoiceList();
            } else {
                // Force a small delay to allow voices to load
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
                status.textContent = 'Error: Voices not loaded. Please wait or refresh the page.';
                return;
            }

            // Get selected voice
            const selectedOption = voiceSelect.selectedOptions[0];
            const selectedVoiceName = selectedOption.getAttribute('data-name');
            const selectedVoice = voices.find(voice => voice.name === selectedVoiceName);

            // Create utterance
            const utterance = new SpeechSynthesisUtterance(text);
            utterance.voice = selectedVoice;
            utterance.rate = parseFloat(rateControl.value);
            utterance.pitch = parseFloat(pitchControl.value);
            utterance.volume = 1;

            // Update UI
            speakButton.classList.add('speaking');
            speakButton.textContent = 'Speaking...';
            stopButton.style.display = 'block';
            status.textContent = `Speaking with ${selectedVoice.name}...`;
            isSpeaking = true;

            // Speech event handlers
            utterance.onend = () => {
                speakButton.classList.remove('speaking');
                speakButton.textContent = 'Speak Text';
                stopButton.style.display = 'none';
                status.textContent = 'Speech finished.';
                isSpeaking = false;
            };

            utterance.onerror = (event) => {
                status.textContent = `Error: ${event.error}. Check audio settings or try a different voice.`;
                speakButton.classList.remove('speaking');
                speakButton.textContent = 'Speak Text';
                stopButton.style.display = 'none';
                isSpeaking = false;
            };

            // Start speaking
            window.speechSynthesis.speak(utterance);
        });

        // Handle stop button click
        stopButton.addEventListener('click', () => {
            window.speechSynthesis.cancel();
            speakButton.classList.remove('speaking');
            speakButton.textContent = 'Speak Text';
            stopButton.style.display = 'none';
            status.textContent = 'Speech stopped.';
            isSpeaking = false;
        });

        // Update status when user types
        textInput.addEventListener('input', () => {
            status.textContent = textInput.value.trim() ? 'Text ready to speak.' : 'Enter text to speak.';
        });
    </script>
</body>
</html>
