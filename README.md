# Audio Signal Demodulation

A Python/Jupyter tool for detecting and decoding **ultrasonic AM-modulated data hidden in audio files**. It scans a batch of WAV files for energy in the 18–22 kHz ultrasonic band (inaudible to humans), demodulates the most suspicious one using a Hilbert-transform envelope detector, and attempts to recover a hidden 4-digit code from the demodulated signal.

## Contents

| File | Description |
|---|---|
| `exercise 3.1.ipynb` | Main notebook: analyzes, filters, demodulates, and decodes all sample audio files. |
| `Ex3_sound1.wav` – `Ex3_sound4.wav` | Input audio samples to be analyzed for hidden ultrasonic content. |
| `demodulated_Ex3_sound1.wav` – `demodulated_Ex3_sound4.wav` | Output: each input file after bandpass filtering + AM demodulation. |

## How It Works

For each sample audio file, the notebook:

1. **Loads and mono-mixes** the audio (stereo files are averaged to mono).
2. **Plots the spectrum and spectrogram** of the raw signal, to visually check for energy at ultrasonic frequencies.
3. **Bandpass filters** the signal to isolate the 18 kHz–22 kHz ultrasonic range using a Butterworth filter (`scipy.signal.butter` + `sosfilt`).
4. **Computes ultrasonic energy** (sum of squared samples in the filtered band) as a suspicion score — the file with the highest ultrasonic energy is flagged as most likely to contain hidden data.
5. **Demodulates** the filtered band using a Hilbert-transform envelope detector (`demodulate_am`), recovering the amplitude envelope that carries the hidden signal.
6. **Saves** each demodulated result to `demodulated_<filename>.wav` and plots the filtered spectrum/spectrogram too.
7. **Extracts a hidden code** from the most suspicious file by peak-picking the demodulated envelope (`extract_hidden_code`) and mapping peak positions to digits, then plays back the recovered audio in-notebook.

## Results

Running the notebook on the four sample files gave:

| File | Ultrasonic Energy | Suspicious? |
|---|---|---|
| `Ex3_sound1.wav` | 16.26 | No |
| `Ex3_sound2.wav` | 0.00 | No |
| `Ex3_sound3.wav` | 0.00 | No |
| `Ex3_sound4.wav` | **487.46** | **Yes — highest energy** |

`Ex3_sound4.wav` was flagged as the carrier, and the extraction step recovered the hidden code **`1891`**.

## Usage

Open `exercise 3.1.ipynb` in Jupyter and run all cells top to bottom. It will:

1. Analyze each file in `SAMPLE_FILES` and plot its spectrum/spectrogram before and after bandpass filtering
2. Print the ultrasonic energy for each file
3. Save a demodulated version of every file
4. Identify the file with the highest ultrasonic energy, play its demodulated audio, and print the recovered 4-digit code

## Skills Demonstrated

- Digital signal processing (filtering, Hilbert transform, spectral/spectrogram analysis)
- Signal demodulation (AM envelope detection)
- Anomaly/energy-based detection across a batch of signals
- Data visualization with Matplotlib
- Python audio I/O
