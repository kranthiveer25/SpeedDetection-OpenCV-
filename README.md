Vehicle Speed Detection and Automatic Gate Control System
Overview
This project is a Computer Vision-based Vehicle Speed Detection System that uses OpenCV's Deep Neural Network (DNN) module and a pre-trained Caffe model to detect vehicles in a video stream, calculate their speed, and automatically control a gate through an Arduino based on a predefined speed limit.
The system tracks vehicles as they move across multiple virtual checkpoints in the video frame. By measuring the time taken to travel between these checkpoints, the vehicle's speed is estimated. Depending on the calculated speed, the system can:

Allow the vehicle to pass (Gate OPEN)
Restrict access (Gate CLOSE)
Log results to a CSV file
Send commands to an Arduino-controlled gate
Features
Vehicle detection using OpenCV DNN and Caffe model
Speed estimation in:
Kilometers per hour (Km/h)
Miles per hour (Mi/h)
Meters per second (m/s)
Automatic gate control based on speed limit
Real-time vehicle tracking
CSV report generation
Arduino integration for physical gate automation
Video file or webcam support
Project Structure
Project/
│
├── main.py
│
├── Support/
│   ├── config.json
│   ├── cars.caffemodel
│   └── cars.prototxt
│
├── TEST-2.mp4
│
└── output.csv
Required Libraries
Install the required Python packages:
pip install opencv-python
pip install numpy
pip install imutils
pip install pyserial
Or:
pip install opencv-python numpy imutils pyserial
Configuration File
The project uses a configuration file located at:
Support/config.json
Example:
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
Configuration Parameters
Parameter	Description
confidence	Minimum confidence score for vehicle detection
quadrant_length	Distance between checkpoints (meters)
speed_limit	Maximum allowed speed (Km/h)
width	Display window width
MI_KM	Kilometer to mile conversion factor
output_csv	Enable CSV report generation
output_csv_file	Output CSV filename
output_arduino	Enable Arduino communication
How It Works
1. Vehicle Detection
The system loads a pre-trained Caffe model:
net = cv2.dnn.readNetFromCaffe(proto_path, model_path)
Each video frame is processed to detect vehicles.
2. Virtual Checkpoints
Three virtual checkpoints divide the frame:
r_q = [
    int(W * 0.25),
    int(W * 0.50),
    int(W * 0.75)
]
Vehicle movement across these checkpoints is tracked.
|----25%----|----50%----|----75%----|
S1          S2          S3
3. Speed Calculation
The frame numbers at which a vehicle crosses each checkpoint are recorded.
Average travel time:

tempi = (
    (tmp_fr_nm[1] - tmp_fr_nm[0]) +
    (tmp_fr_nm[2] - tmp_fr_nm[1])
) / 2
Speed is calculated using:
m = quadrant_length / (time_taken * time_per_frame)
Conversions:
km = m * 6
mi = km * MI_KM
4. Gate Control Logic
If vehicle speed is below the configured speed limit:
gate_flag = "OPEN"
Otherwise:
gate_flag = "CLOSE"
5. Arduino Communication
When enabled:
ser = serial.Serial('COM4', 115200)
Commands sent:
Command	Action
O	Open Gate
C	Close Gate
Example:
ser.write(bytes("O\n", "ascii"))
Using Webcam Instead of Video File
Current:
cap = cv2.VideoCapture('TEST-2.mp4')
For webcam:
cap = cv2.VideoCapture(0)
Output Example
Console Output:
Details of Past Car:

Car Id 1

speed of the car = 15.4 M/s

speed of the car in Metric.std = 55.4 KM/hr

speed of the car in Imperial.std = 34.4 MI/hr

Gate Control Status: OPEN
CSV Output
Generated file:
SN,Date,Speed(KPH),Speed(MIH),Gate_status
1,20/04/2025 15:32:11,55.4 Km/hr,34.4 Mi/hr,OPEN
2,20/04/2025 15:32:45,72.1 Km/hr,44.8 Mi/hr,CLOSE
Detection Pipeline
Video Input
      │
      ▼
Frame Capture
      │
      ▼
Vehicle Detection
      │
      ▼
Checkpoint Tracking
      │
      ▼
Speed Calculation
      │
      ▼
Speed Limit Check
      │
      ▼
 ┌─────────────┐
 │ OPEN GATE   │
 │ CLOSE GATE  │
 └─────────────┘
      │
      ▼
CSV Logging
Applications
Smart Toll Gates
Parking Entry Systems
Industrial Vehicle Monitoring
Traffic Surveillance
Smart City Transportation Systems
Campus Security Gates
Future Enhancements
Multi-vehicle tracking with unique IDs
License plate recognition (ANPR)
Real-time dashboard visualization
Cloud database integration
Email/SMS alerts for overspeeding vehicles
YOLOv8-based vehicle detection
Vehicle classification (Car, Bus, Truck, Motorcycle)
Author
Vehicle Speed Detection and Automatic Gate Control System
Built using:

Python
OpenCV DNN
NumPy
Imutils
Arduino Serial Communication
Caffe Deep Learning Model
License
This project is intended for educational and research purposes. Modify and distribute according to your project requirements.
Meet Codex.
A coding agent that helps you build and ship with AI, included for free in your ChatGPT plan.

Download the app

Learn more





