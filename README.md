#  Real-Time Vision-Based Gesture Controlled Media System  
### Touchless Human-Computer Interaction on NVIDIA Jetson Nano (Edge AI Optimized)

---

#  Project Overview

This project presents a real-time, touchless Human-Computer Interaction (HCI) system designed and optimized for embedded ARM-based edge hardware. The system enables intuitive control of the MPV media player using predefined hand gestures detected via a live camera feed.

Unlike cloud-dependent gesture systems, this solution performs the complete AI inference pipeline locally on the NVIDIA Jetson Nano. The system is engineered to achieve low latency, stable frame rate, and reliable gesture recognition under embedded hardware constraints.

The core objective is to demonstrate that practical, real-time gesture-driven interaction can be achieved on a resource-constrained ARM-based platform through intelligent system optimization and efficient computer vision design.

---

#  System Architecture Overview

The system follows a structured real-time processing pipeline:

1. **Frame Capture Layer**
   - USB camera input using V4L2 backend
   - Resolution optimized to 320×240
   - Buffer size set to 1 to eliminate frame delay

2. **Preprocessing Layer**
   - Frame resizing to 224×168 before inference
   - Color space conversion (BGR → RGB)
   - Writable flag optimization

3. **Hand Detection & Landmark Extraction**
   - MediaPipe Hands framework
   - 21 hand landmark detection
   - Configured with `model_complexity=0`
   - Single-hand detection to maintain FPS

4. **Gesture Classification Module**
   - Rule-based geometric logic
   - Finger state computation (up/down)
   - Distance-based gesture recognition
   - Wrist-zone-based navigation control
   - Palm orientation verification
   - Cooldown-based trigger protection

5. **Command Mapping Layer**
   - Gesture mapped to MPV keyboard shortcuts
   - xdotool executed via subprocess.Popen (non-blocking)

6. **Output Layer**
   - MPV media player control
   - Real-time FPS and feedback display

The entire system runs locally on the Jetson Nano without internet dependency.

---

#  Hardware Platform

## NVIDIA Jetson Nano Developer Kit (4GB)

- SoC: NVIDIA Tegra X1
- CPU: Quad-core ARM Cortex-A57
- GPU: 128-core NVIDIA Maxwell
- RAM: 4GB LPDDR4
- Storage: microSD (JetPack OS)
- Power: 5V 4A Barrel Jack (Recommended)

## Additional Components
- USB Webcam (Configured at 320×240 resolution)
- HDMI Display
- Cooling Fan + Heatsink
- USB Keyboard & Mouse

This hardware configuration ensures stable real-time processing without thermal throttling or voltage drop.

---

#  Software Stack

- NVIDIA JetPack SDK (Ubuntu ARM64)
- Python 3
- OpenCV (V4L2 backend)
- MediaPipe Hands
- MPV Media Player
- xdotool (Linux key simulation)
- CUDA Toolkit

The software architecture is optimized for ARM CPU inference and embedded AI efficiency.

---

#  Gesture Control Mapping

| Gesture | Recognition Logic | Action |
|----------|------------------|--------|
| Wide Open Palm | 5 fingers open & spread | Play / Pause |
| Index + Pinky (Left Zone) | Wrist X < 35% | Seek Backward (10s) |
| Index + Pinky (Right Zone) | Wrist X > 65% | Seek Forward (10s) |
| Thumb + Index | Both raised | Volume Up |
| OK Sign | Thumb touching Index | Volume Down |
| Index + Middle | Thumb near ring finger | Mute |
| 3 Fingers Raised | Index, Mid, Ring | Fullscreen |
| Thumb + Pinky | Both raised | Subtitle Toggle |
| Back of Hand | Palm not facing camera | Ignored |

Robustness Enhancements:
- Back-of-hand rejection using landmark 5 & 17 comparison
- Cooldown timers for toggle-based actions
- Interval-based rate limiting for continuous actions
- Asynchronous key triggering

---

#  Installation

## 1. Flash JetPack OS
- Write JetPack image to microSD
- Insert into Jetson Nano
- Use 5V 4A barrel jack for stable power

## 2. Update System

```bash
sudo apt update
sudo apt upgrade
