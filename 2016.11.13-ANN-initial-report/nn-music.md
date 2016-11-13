title: Chord detection and music transcription with neural networks
speaker: Alex Wang
url: http://soappt.a1ex.wang
transition: cards
theme: light
files: /js/demo.js,/assets/main.css

[slide]
# Chord detection and music transcription with ANN

[slide]
## Goals
------
- Chord detection
- Chroma
- Music transcription

[slide]
## Our goals
------
- Solo piano music
- Polyphonic pitch detection
- {wav, mp3,...} -> MIDI
- Onset/offset time, pitch

[slide]
## State-of-art performance
- AnthemScore's method in MIREX 2016
- Piano solo pitch detection chroma result
- accuracy 91.2%
- recall 75.6%
- F-score 82.0%

[slide]
## Dataset
------
- MAPS database(31G piano music in CD quality, tagged with MIDI)
- Maestro Concert Grand v2 + MIDI
- www.piano-midi.de

[slide]
## How to do it
------
1. preprocessing
2. cnn, with
3. postprocesssing

[slide]
# Preprocessing
------
- Energy based onset detection
- Constant-Q/Dynamic-Q transformation(fixed length interval)
- Cut out onsets
![constant-q](/assets/constant-q.png)

[slide]
# Neural network
------
1. Harmonics are not localized
2. 1D CNN or 2D CNN
3. CNN with shortcuts
![cnn](/assets/cnn.png)

[slide]
# Postprocessing
------
1. Connect broken notes
2. Beats detection and auto alignment

[slide]
## Agenda
- 12.04 checkpoint
- 12.27 final presentation
