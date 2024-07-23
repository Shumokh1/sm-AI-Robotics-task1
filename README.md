# sm-AI-Robotics-task1
# Real-Time Face Detection with Flask and OpenCV

## Overview

Task1: Use OpenCV and flask to make real time face detection

This task shows a simple web application that performs real-time face detection using Flask and OpenCV. The application captures video from a webcam, processes the video frames to detect faces, and streams the processed video to a web browser.

## Demo

## Installation

Follow these steps to set up the project on your local machine.

### Steps

1. **Create a Project Folder**:
   - Open a terminal and create a new folder for your project:
   
     ```bash
     mkdir face_detection_flask
     cd face_detection_flask
     ```

2. **Open Visual Studio Code**:
   - Open Visual Studio Code and open the folder you just created:
     - Click on `File` -> `Open Folder...`
     - Navigate to the `face_detection_flask` folder and open it.

3. **Create `app.py` File**:
   - Inside the `face_detection_flask` folder, create a new file named `app.py`:
     - Right-click on the folder name in the Visual Studio Code Explorer pane.
     - Select `New File` and name it `app.py`.

4. **Copy and Paste the Code**:
   - Copy the following code and paste it into the `app.py` file:

     ```python
     from flask import Flask, Response
     import cv2

     app = Flask(__name__)

     # Load the pre-trained face detection model
     face_cascade = cv2.CascadeClassifier(cv2.data.haarcascades + 'haarcascade_frontalface_default.xml')

     def generate_frames():
         # Open the video capture (0 for default webcam)
         cap = cv2.VideoCapture(0)
         
         while True:
             # Read frame-by-frame
             success, frame = cap.read()
             if not success:
                 break
             
             # Convert to grayscale for face detection
             gray = cv2.cvtColor(frame, cv2.COLOR_BGR2GRAY)
             
             # Detect faces
             faces = face_cascade.detectMultiScale(gray, scaleFactor=1.1, minNeighbors=5, minSize=(30, 30))
             
             # Draw rectangles around faces
             for (x, y, w, h) in faces:
                 cv2.rectangle(frame, (x, y), (x+w, y+h), (255, 0, 0), 2)
             
             # Encode frame as JPEG
             ret, buffer = cv2.imencode('.jpg', frame)
             frame = buffer.tobytes()
             
             # Yield the frame in the format expected by the client
             yield (b'--frame\r\n'
                    b'Content-Type: image/jpeg\r\n\r\n' + frame + b'\r\n')

     @app.route('/')
     def index():
         return '''
             <!DOCTYPE html>
             <html lang="en">
             <head>
                 <meta charset="UTF-8">
                 <meta name="viewport" content="width=device-width, initial-scale=1.0">
                 <title>Real-Time Face Detection</title>
             </head>
             <body>
                 <h1>Real-Time Face Detection</h1>
                 <img src="/video_feed" width="640" height="480">
             </body>
             </html>
         '''

     @app.route('/video_feed')
     def video_feed():
         return Response(generate_frames(),
                         mimetype='multipart/x-mixed-replace; boundary=frame')

     if __name__ == '__main__':
         app.run(debug=True)
     ```

5. **Install Dependencies**:
   - Open a terminal in Visual Studio Code:
     - Click on `Terminal` -> `New Terminal`
   - Install the required Python packages by running:

     ```bash
     pip install flask opencv-python
     ```

6. **Run the Flask Application**:
   - Run the application by typing:

     ```bash
     python app.py
     ```

7. **Access the Web Application**:
   - The output will show a URL, typically `http://127.0.0.1:5000/`.
   - Copy and paste this URL into your web browser to view the application.

### Troubleshooting

- If the first time you run the URL it asks for camera permission, you should click "Allow" to grant access to the webcam. If you encounter this issue, rerun the `python app.py` command after allowing camera access.



