<div align="center">

# 🖐️ Gesture Music Controller

**Control music playback and volume in real time using hand gestures — powered by a custom-trained CNN and OpenCV.**

![Python](https://img.shields.io/badge/Python-3.9%2B-3776AB?style=for-the-badge&logo=python&logoColor=white)
![PyTorch](https://img.shields.io/badge/PyTorch-CNN-EE4C2C?style=for-the-badge&logo=pytorch&logoColor=white)
![OpenCV](https://img.shields.io/badge/OpenCV-RealTime-5C3EE8?style=for-the-badge&logo=opencv&logoColor=white)
![PyAutoGUI](https://img.shields.io/badge/PyAutoGUI-Automation-000000?style=for-the-badge)
![License](https://img.shields.io/badge/License-MIT-green?style=for-the-badge)

</div>

---

**Complete notebook + Data + Trained Model:** 
https://www.kaggle.com/code/atharvgupta068/hand-gesture-controler

## 📋 Overview

This project turns a webcam into a touchless media remote. A CNN trained on hand-gesture images classifies frames in real time, and OpenCV drives the live inference loop. When a gesture is recognized with high confidence, `pyautogui` simulates the matching media key — no keyboard or mouse required.

## 🔄 How It Works

```
  Webcam Frame  →  Crop Hand Region  →  CNN Prediction  →  Confidence Check  →  Simulated Key Press
                                              │
                              ┌───────────────┼───────────────┐
                              ▼               ▼               ▼
                           01_palm         05_thumb         10_down
                          (Play/Pause)    (Volume Up)     (Volume Down)
```

| Stage | Purpose |
|---|---|
| **Video Capture** | Reads frames from the webcam (`cv2.VideoCapture`), with automatic fallback between camera indexes |
| **Hand Region Crop** | Crops a centered box from each frame where the user places their hand |
| **Preprocessing** | Resizes to `128×128`, converts to tensor, normalizes — matches training pipeline |
| **CNN Model** | 3-class convolutional network predicts the gesture from the cropped region |
| **Confidence Gate** | Only acts on predictions above **70%** confidence |
| **Cooldown** | Enforces a **1-second** delay between actions to prevent repeat triggers |
| **PyAutoGUI** | Sends the OS-level media key press (`space`, `volumeup`, `volumedown`) |
| **Overlay UI** | Displays the live gesture, confidence score, and triggered action on-screen |

## ✋ Supported Gestures

<div align="center">

| Gesture | Class Name | Action | Key Simulated |
|:---:|:---:|:---:|:---:|
| 🖐️ Palm | `01_palm` | Play / Pause | `space` |
| 👍 Thumb | `05_thumb` | Volume Up | `volumeup` |
| 👇 Down | `10_down` | Volume Down | `volumedown` |

</div>

## ✨ Features

- 🎥 **Real-time gesture recognition** via webcam using a PyTorch CNN
- 🙌 **Touchless media control** — no keyboard or mouse needed
- 📊 **Live overlay** showing detected gesture, confidence score, and current action
- ⏱️ **Action cooldown** to prevent repeated/accidental triggers
- 🔌 **Camera fallback** — tries a secondary camera (e.g. DroidCam) before defaulting to the primary webcam

## 🧠 Model

A CNN trained from scratch on a hand-gesture image dataset:

| Property | Detail |
|---|---|
| Architecture | 3 conv blocks (32 → 64 → 128 channels), each with ReLU + MaxPool |
| Input size | `128×128`, normalized to `[-1, 1]` |
| Output | 3 gesture classes |
| Training | 9 epochs, Adam optimizer, Cross-Entropy loss |

Training code lives in [`ai_hand_gestures_controller.ipynb`](./ai_hand_gestures_controller.ipynb). The trained weights (`hand_gesture_cnn.pth`) must sit in the same directory as `app.py` for inference to work.

> **Note:** The notebook trains on a fixed random subset (`sample_size = 30000`) with an 80/20 train/validation split. Point `dataset_path` at your own dataset before retraining.

## 🧰 Requirements

- Python 3.9+
- A webcam (or a virtual camera app like DroidCam)
- GPU optional — uses CUDA automatically if available, otherwise falls back to CPU

## 🚀 Setup Instructions

1. **Clone the repository**
   ```bash
   git clone https://github.com/<zakir-maswani>/AI-Hand-Gesture-Controler.git
   cd gesture-music-controller
   ```

2. **Create a virtual environment** *(recommended)*
   ```bash
   python -m venv venv

   # Windows
   venv\Scripts\activate

   # macOS/Linux
   source venv/bin/activate
   ```

3. **Install dependencies**
   ```bash
   pip install -r requirements.txt
   ```

4. **Add the trained model weights**
   Place `hand_gesture_cnn.pth` in the project root, next to `app.py`. Train your own via the notebook if you don't have one.

5. **Check the camera index in `app.py`**
   Defaults to index `1` (e.g. DroidCam) with a fallback to `0`. If you only have one webcam, change this to `cv2.VideoCapture(0)`.

6. **Run the app**
   ```bash
   python app.py
   ```

7. **Test each gesture** in front of the camera and confirm the correct action fires before relying on it during playback.

## ▶️ Usage

- A webcam window opens with a target box — place your hand inside it.
- The top status bar shows the detected gesture, confidence score, and triggered action.
- Press **`q`** on the camera window to quit.

## 📁 Repository Structure

```
├── my_cv_app.py                             
├── ai_hand_gestures_controller.ipynb   
├── hand_gesture_cnn.pth                
├── requirements.txt
├── README.md
├── gesture_controller_demo.mp4
└── inference.ipynb

```

> `hand_gesture_cnn.pth` isn't included in this repo (weight files are typically large). Train it yourself via the notebook, or drop in your own file with that name.

## ⚠️ Notes & Limitations

- Works best with a plain, well-lit background and the hand centered in the target box.
- `pyautogui` simulates media keys — actual behavior depends on your OS and active media session.
- Currently supports 3 gestures only; adding more requires retraining the CNN and updating `CLASS_NAMES` in `app.py`.
  
---

<div align="center">
Made with 🖐️ using PyTorch + OpenCV + PyAutoGUI
</div>
