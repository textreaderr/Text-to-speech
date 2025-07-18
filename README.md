# Text-to-speech
A program that takes texts and reads it to you
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Text-to-Speech Interface (Chris Hansen Style)</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            height: 100vh;
            margin: 0;
            background-color: #f0f0f0;
            color: #333;
        }
        h1 {
            font-size: 2.5em;
            margin-bottom: 20px;
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
            margin-bottom: 10px;
        }
        #voice-select {
            width: 80%;
            max-width: 600px;
            padding: 10px;
            font-size: 1.2em;
            margin-bottom: 10px;
            border-radius: 8px;
        }
        #speak-button {
            padding: 10px 20px;
            font-size: 1.2em;
            background-color: #007bff;
            color: white;
            border: none;
            border-radius: 8px;
            cursor: pointer;
            transition: background-color 0.3s;
        }
        #speak-button:hover {
            background-color: #0056b3;
        }
        #speak-button.speaking {
            background-color: #dc3545;
        }
        #status {
            margin-top: 10px;
            font-size: 1.1em;
            color: #555;
            text-align: center;
        }
        @media (max-width: 600px) {
            h1 {
                font-size: 1.8em;
            }
            #text-input, #voice-select {
                width: 90%;
                height: 100px;
            }
            #speak-button {
                font-size: 1em;
                padding: 8px 16px;
            }
        }
    </style>
</head>
<body>
    <h1>Text-to-Speech Interface</h1>
    <textarea id="text-input" placeholder="Type or paste text to speak..."></textarea>
    <select id="voice-select">
        <option value="">Select a voice</option>
    </select>
    <button id="speak-button">Speak Text</button>
    <div id="status">Enter text and click the button to hear it spoken</div>

    <script>
        const speakButton = document.getElementById('speak-button');
        const textInput = document.getElementById('text-input');
        const voiceSelect = document.getElementById('voice-select');
        const status = document.getElementById('status');

        // Check if Web Speech API (SpeechSynthesis) is available
        if (!window.speechSynthesis) {
            status.textContent = 'Sorry, your browser does not support text-to-speech. Try Chrome or Safari.';
            speakButton.disabled = true;
            voiceSelect.disabled = true;
            return;
        }

        let isSpeaking = false;
        let voices = [];
        let defaultVoiceIndex = null;

        // Load voices and set default voice
        function loadVoices() {
            voices = window.speechSynthesis.getVoices();
            voiceSelect.innerHTML = '<option value="">Select a voice</option>';
            if (voices.length === 0) {
                status.textContent = 'No voices available. Try a different browser (e.g., Chrome).';
                speakButton.disabled = true;
                return;
            }

            // Prioritize a male, American English voice to approximate Chris Hansen
            voices.forEach((voice, index) => {
                const option = document.createElement('option');
                option.value = index;
                option.textContent = `${voice.name} (${voice.lang})`;
                voiceSelect.appendChild(option);

                // Set default voice: prefer male, en-US voices (e.g., Google US English Male, Microsoft David)
                if (defaultVoiceIndex === null && voice.lang.includes('en') &&
                    (voice.name.toLowerCase().includes('male') || voice.name.includes('David') || voice.name.includes('Mark'))) {
                    defaultVoiceIndex = index;
                    voiceSelect.value = index;
                    status.textContent = `Default voice set to ${voice.name}. Enter text to speak.`;
                }
            });

            // Fallback to first en-US voice or first available voice
            if (defaultVoiceIndex === null) {
                defaultVoiceIndex = voices.findIndex(voice => voice.lang.includes('en')) || 0;
                voiceSelect.value = defaultVoiceIndex;
                status.textContent = `Default voice set to ${voices[defaultVoiceIndex].name}. Enter text to speak.`;
            }
        }

        // Voices may load asynchronously, so wait for them
        window.speechSynthesis.onvoiceschanged = loadVoices;
        loadVoices(); // Try loading voices immediately

        // Handle speak button click
        speakButton.addEventListener('click', () => {
            const text = textInput.value.trim();

            // If already speaking, cancel the current speech
            if (isSpeaking) {
                window.speechSynthesis.cancel();
                speakButton.textContent = 'Speak Text';
                speakButton.classList.remove('speaking');
                status.textContent = 'Speech stopped.';
                isSpeaking = false;
                return;
            }

            // Check if there is text to speak
            if (!text) {
                status.textContent = 'Please enter some text to speak.';
                return;
            }

            // Check if voices are loaded
            if (voices.length === 0) {
                status.textContent = 'No voices loaded. Please try again or use Chrome/Safari.';
                return;
            }

            // Create a new SpeechSynthesisUtterance
            const utterance = new SpeechSynthesisUtterance(text);
            utterance.lang = 'en-US';
            utterance.volume = 1; // 0 to 1
            utterance.rate = 0.9; // Slightly slower for authoritative tone
            utterance.pitch = 0.8; // Slightly lower for deeper tone

            // Set voice: use selected voice or default
            const selectedVoiceIndex = voiceSelect.value;
            if (selectedVoiceIndex !== '') {
                utterance.voice = voices[selectedVoiceIndex];
                status.textContent = `Speaking with ${voices[selectedVoiceIndex].name}...`;
            } else {
                utterance.voice = voices[defaultVoiceIndex];
                status.textContent = `Speaking with default voice (${voices[defaultVoiceIndex].name})...`;
            }

            // Update UI when speech starts
            speakButton.textContent = 'Stop Speaking';
            speakButton.classList.add('speaking');
            isSpeaking = true;

            // When speech ends, reset UI
            utterance.onend = () => {
                speakButton.textContent = 'Speak Text';
                speakButton.classList.remove('speaking');
                status.textContent = 'Speech finished.';
                isSpeaking = false;
            };

            // Handle errors
            utterance.onerror = (event) => {
                status.textContent = `Speech error: ${event.error}. Check audio settings or try a different voice/browser.`;
                speakButton.textContent = 'Speak Text';
                speakButton.classList.remove('speaking');
                isSpeaking = false;
            };

            // Start speaking
            window.speechSynthesis.speak(utterance);
        });

        // Update status when user types
        textInput.addEventListener('input', () => {
            status.textContent = textInput.value.trim() ? 'Text ready to speak.' : 'Enter text to speak.';
        });

        // Update status when voice is selected
        voiceSelect.addEventListener('change', () => {
            const selectedIndex = voiceSelect.value;
            status.textContent = selectedIndex ? `Voice selected: ${voices[selectedIndex].name}. Enter text to speak.` : 'Using default voice. Enter text to speak.';
        });
    </script>
</body>
</html>
