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
            max-width: 80%;
        }
        @media (max-width: 600px) {
            h1 {
                font-size: 1.8em;
            }
            #text-input {
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
    <button id="speak-button">Speak Text</button>
    <div id="status">Enter text and click the button to hear it spoken</div>

    <script>
        const speakButton = document.getElementById('speak-button');
        const textInput = document.getElementById('text-input');
        const status = document.getElementById('status');

        // Check if Web Speech API (SpeechSynthesis) is available
        if (!window.speechSynthesis) {
            status.textContent = 'Error: Browser does not support text-to-speech. Try Chrome or Safari.';
            speakButton.disabled = true;
            return;
        }

        let isSpeaking = false;
        let voices = [];
        let defaultVoice = null;

        // Load voices and set default voice
        function loadVoices() {
            voices = window.speechSynthesis.getVoices();
            if (voices.length === 0) {
                status.textContent = 'Error: No voices available. Try Chrome or Safari, or check device settings.';
                speakButton.disabled = true;
                return;
            }

            // Prioritize a male, American English voice to approximate Chris Hansen
            defaultVoice = voices.find(voice => 
                voice.lang.includes('en') && 
                (voice.name.toLowerCase().includes('male') || 
                 voice.name.includes('David') || 
                 voice.name.includes('Mark'))
            ) || voices.find(voice => voice.lang.includes('en')) || voices[0];

            status.textContent = `Voice set to ${defaultVoice.name}. Enter text to speak.`;
            speakButton.disabled = false;
        }

        // Handle asynchronous voice loading
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
            if (voices.length === 0 || !defaultVoice) {
                status.textContent = 'Error: Voices not loaded. Try refreshing or using Chrome/Safari.';
                return;
            }

            // Create a new SpeechSynthesisUtterance
            const utterance = new SpeechSynthesisUtterance(text);
            utterance.lang = 'en-US';
            utterance.volume = 1; // 0 to 1
            utterance.rate = 0.9; // Slightly slower for authoritative tone
            utterance.pitch = 0.8; // Lower for deeper tone
            utterance.voice = defaultVoice;

            // Update UI when speech starts
            speakButton.textContent = 'Stop Speaking';
            speakButton.classList.add('speaking');
            status.textContent = `Speaking with ${defaultVoice.name}...`;
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
                status.textContent = `Error: ${event.error}. Check audio (unmute, check headphones), or try Chrome/Safari.`;
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
    </script>
</body>
</html>
