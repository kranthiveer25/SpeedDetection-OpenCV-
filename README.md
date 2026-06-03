# Vehicle Speed Detection and Automatic Gate Control System

## Overview

The Vehicle Speed Detection and Automatic Gate Control System is a computer vision-based application that detects vehicles in a video stream, estimates their speed, and controls a gate automatically based on a predefined speed limit.

The system uses OpenCV's Deep Neural Network (DNN) module with a pre-trained Caffe model to identify vehicles in each frame. As a vehicle moves through predefined virtual checkpoints, the application records the frame positions and calculates its speed. Depending on whether the detected speed is within the allowed limit, the system either opens or closes a gate through Arduino communication.

Additionally, the system can generate CSV reports containing vehicle speed records for future analysis.

---

# Features

* Real-time vehicle detection using OpenCV DNN.
* Speed calculation in:

  * Kilometers per hour (Km/h)
  * Miles per hour (Mi/h)
  * Meters per second (m/s)
* Automatic gate control using Arduino.
* Vehicle tracking through virtual checkpoints.
* CSV report generation.
* Webcam and video file support.
* Configurable speed limits and detection settings.

---

# Project Structure

```
Project Directory
│
├── main.py
│
├── Support
│   ├── config.json
│   ├── cars.caffemodel
│   └── cars.prototxt
│
├── TEST-2.mp4
│
└── output.csv
```

---

# System Requirements

## Hardware Requirements

* Computer/Laptop
* USB Camera or Webcam (optional)
* Arduino Board (optional)
* Automated Gate Mechanism (optional)

## Software Requirements

* Python 3.x
* OpenCV
* NumPy
* Imutils
* PySerial

---

# Installation

Install all required dependencies using pip:

```bash
pip install opencv-python numpy imutils pyserial
```

Alternatively:

```bash
pip install opencv-python
pip install numpy
pip install imutils
pip install pyserial
```

---

# Configuration

The application settings are stored in:

```
Support/config.json
```

Example configuration:

```json
{
    "confidence": 0.4,
    "quadrant_length": 5,
    "speed_limit": 60,
    "width": 800,
    "MI_KM": 0.621371,
    "output_csv": true,
    "output_csv_file": "output.csv",
    "output_arduino": false
}
```

## Configuration Parameters

| Parameter       | Description                                       |
| --------------- | ------------------------------------------------- |
| confidence      | Minimum confidence required for vehicle detection |
| quadrant_length | Distance between virtual checkpoints (meters)     |
| speed_limit     | Maximum allowed speed in Km/h                     |
| width           | Display frame width                               |
| MI_KM           | Kilometer-to-mile conversion factor               |
| output_csv      | Enables CSV output                                |
| output_csv_file | CSV file name                                     |
| output_arduino  | Enables Arduino communication                     |

---

# Working Principle

## Step 1: Video Input

The system captures frames from either:

* A video file
* A webcam

Current configuration:

```python
cap = cv2.VideoCapture('TEST-2.mp4')
```

To use a webcam:

```python
cap = cv2.VideoCapture(0)
```

---

## Step 2: Vehicle Detection

A pre-trained Caffe model is loaded using OpenCV DNN:

```python
net = cv2.dnn.readNetFromCaffe(proto_path, model_path)
```

Each frame is converted into a blob and passed through the neural network for object detection.

Only vehicles belonging to the specified class ID are processed.

---

## Step 3: Vehicle Tracking

The frame width is divided into three virtual checkpoints:

```python
r_q = [
    int(W * 0.25),
    int(W * 0.50),
    int(W * 0.75)
]
```

Visualization:

```
-----------------------------------------------------
|         S1          S2           S3               |
-----------------------------------------------------
     25%         50%          75%
```

The center position of each detected vehicle is continuously monitored.

When the vehicle crosses:

* Checkpoint S1 → Frame number stored
* Checkpoint S2 → Frame number stored
* Checkpoint S3 → Frame number stored

These frame numbers are used for speed estimation.

---

## Step 4: Speed Calculation

The average time required to travel between checkpoints is calculated.

Formula:

```
Average Frames =
((FrameS2 - FrameS1) + (FrameS3 - FrameS2)) / 2
```

Speed in meters per second:

```
Speed = Distance / Time
```

Implemented as:

```python
m = quadrant_length / (time_taken * time_per_frame)
```

Conversions:

```python
km = m * 6
mi = km * MI_KM
```

Outputs:

* Speed (m/s)
* Speed (Km/h)
* Speed (Mi/h)

---

## Step 5: Gate Decision Logic

The calculated speed is compared with the configured speed limit.

```python
if km < speed_limit:
    gate_flag = "OPEN"
else:
    gate_flag = "CLOSE"
```

### Gate Status

| Condition         | Gate Action |
| ----------------- | ----------- |
| Speed below limit | OPEN        |
| Speed above limit | CLOSE       |

---

## Step 6: Arduino Communication

When Arduino output is enabled:

```python
ser = serial.Serial('COM4', 115200, timeout=1)
```

The system sends commands through the serial port.

### Commands

| Command | Description |
| ------- | ----------- |
| O       | Open Gate   |
| C       | Close Gate  |

Examples:

```python
ser.write(bytes("O\n", "ascii"))
```

```python
ser.write(bytes("C\n", "ascii"))
```

---

# Output

## Console Output

Example:

```
Details of Past Car:

Car Id 1

Speed of the car = 15.20 m/s

Speed of the car in Metric Standard = 54.72 Km/h

Speed of the car in Imperial Standard = 34.00 Mi/h

Gate Control Status = OPEN
```

---

## CSV Output

If CSV logging is enabled, a report is generated automatically.

Example:

```csv
SN,Date,Speed(KPH),Speed(MIH),Gate_status
1,20/04/2025 15:32:11,54.72 Km/hr,34.00 Mi/hr,OPEN
2,20/04/2025 15:33:20,72.40 Km/hr,45.00 Mi/hr,CLOSE
```

CSV Columns:

* SN – Vehicle Number
* Date – Detection Date and Time
* Speed(KPH) – Speed in Kilometers per Hour
* Speed(MIH) – Speed in Miles per Hour
* Gate_status – Gate Decision

---

# Processing Flow

```
Video Input
     │
     ▼
Frame Capture
     │
     ▼
Vehicle Detection
     │
     ▼
Vehicle Tracking
     │
     ▼
Checkpoint Crossing Detection
     │
     ▼
Speed Calculation
     │
     ▼
Speed Limit Verification
     │
     ▼
 ┌───────────────┐
 │ Gate Control  │
 └───────────────┘
     │
     ▼
CSV Logging
```

---

# Applications

* Smart Toll Booth Systems
* Automated Parking Gates
* Campus Entry Monitoring
* Industrial Vehicle Access Control
* Traffic Monitoring Systems
* Smart Transportation Solutions

---

# Future Enhancements

1. Multi-vehicle tracking support.
2. License Plate Recognition (ANPR).
3. YOLO-based object detection.
4. Live monitoring dashboard.
5. Cloud database integration.
6. SMS and Email notifications.
7. Vehicle type classification.
8. Real-time analytics and reporting.

---

# Conclusion

This project demonstrates the integration of Computer Vision, Deep Learning, and Embedded Systems to create an intelligent vehicle speed monitoring solution. By automatically detecting vehicles, estimating their speed, and controlling gate operations, the system provides a practical framework for traffic monitoring, security checkpoints, parking management, and smart city applications.
