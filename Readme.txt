# Real-Time Finger Counting Using Computer Vision (OpenCV)

This project implements a **real-time finger counting system** using a webcam feed and classic computer-vision techniques in **Python + OpenCV**.  
The pipeline detects **skin regions**, reduces noise, extracts the **hand contour**, and estimates the number of raised fingers using **convex hull + convexity defects**.

---

## Overview

The program:
- Captures live video from the default camera.
- Detects skin in the frame using an **HSV color threshold**.
- Applies **noise reduction and morphological operations** to improve the hand mask.
- Finds the largest contour (assumed to be the hand).
- Computes the **convex hull** and **convexity defects**.
- Uses defect angles and depth heuristics to estimate **finger count**.
- Displays intermediate windows and prints the detected (smoothed) finger count.

---

## Pipeline (How it works)

### 1) Video Capture
- Opens webcam using `cv2.VideoCapture(0)`
- Flips the frame horizontally for natural interaction (`cv2.flip(frame, 1)`)

### 2) Skin Detection (HSV Threshold)
Function: `detect_skin(frame, hsv)`
- Converts frame to HSV.
- Applies skin color range:
  - `lower_skin = [0, 20, 70]`
  - `upper_skin = [20, 255, 255]`
- Produces a binary skin mask using `cv2.inRange()`

### 3) Noise Processing (Mask Refinement)
Function: `advanced_noise_processing(mask)`
Applies:
- Gaussian blur to smooth noise.
- Morphological **closing** to fill small holes.
- Morphological **opening** to remove small noise.
- Adaptive thresholding.
- Morphological gradient to emphasize edges.
- Area filtering: keeps only regions with area > `500`.

Output: a cleaner binary mask representing the hand.

### 4) Finger Counting (Convexity Defects)
Function: `count_fingers(hand_mask, output_frame=None)`
Steps:
- Finds contours and selects the largest one as the hand.
- Computes convex hull.
- Computes convexity defects.
- For each defect, calculates the angle at the defect point (cosine rule).
- Counts a finger when:
  - angle < 90 degrees
  - defect depth `d > 10000` (heuristic)

It draws:
- Hand contour
- Defect points
- Text label for the number of detected fingers

### 5) Temporal Smoothing
A small history buffer (deque) averages the last few detections to reduce flicker.

---

## Requirements

- Python 3.8+ (recommended)
- OpenCV
- NumPy

Install dependencies:
```bash
pip install opencv-python numpy
