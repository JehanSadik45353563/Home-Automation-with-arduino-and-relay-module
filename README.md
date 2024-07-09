Home Automation with Arduino and relay module

Project Overview:
The Gesture-Controlled Home Automation System is an innovative project that combines computer vision, Arduino, and home automation to enable users to control household appliances, specifically light bulbs, through hand gestures captured via a webcam. The project aims to enhance user convenience, improve accessibility, and demonstrate the practical applications of gesture recognition technology in everyday life.

Components Used:
The project incorporates several key components:
•	Arduino Board: This serves as the central controller for the system. It communicates with the relay module and handles the logic for processing hand gestures.
•	Relay Module: The relay module acts as a switch to control high-voltage electrical devices. It is connected to the Arduino board, enabling the control of appliances.
•	Webcam: A standard webcam is used to capture live video frames. These frames are then processed to track hand gestures and determine user intent.
•	Computer: The project code runs on a computer, providing the necessary processing power and interface for interaction.
•	Electrical Devices: In the context of this project, light bulbs are controlled. However, the system can be expanded to manage various other electrical appliances, such as fans and home 
automation systems.

Code and Circuit Implementation:
The Gesture-Controlled Home Automation System project combines software and hardware components to create a seamless user experience. The project leverages Python for its software, integrating computer vision, while the hardware side employs an Arduino Uno and a relay module to control electrical appliances, such as light bulbs. Heres a breakdown of the code and circuit implementation:
•	Software Component:
The software aspect of the project relies heavily on computer vision techniques. OpenCV, a popular computer vision library, is used to capture video frames from a webcam, process images, and overlay graphical elements on the video feed. MediaPipe, a library that specializes in hand tracking and landmark detection, enables the system to recognize and track users hand gestures.
Within the code, a class named handDetector is defined to encapsulate the hand tracking and landmark detection functionality. This class makes use of the MediaPipe Hands module to detect the users hand within the video feed. It offers methods for finding hands and their positions, which are crucial for identifying specific hand gestures.
•	Hardware Component:
The hardware side of the project centers around the Arduino Uno microcontroller and a relay module. The Arduino Uno serves as the central controller, facilitating communication between the software and the relay module. The relay module is responsible for controlling electrical appliances, acting as a switch to turn them on or off.
The success of the system depends on a well-designed circuit that connects the Arduino Uno to the relay module. This circuit ensures that the digital pins of the Arduino can effectively control the appliances. Each digital pin on the Arduino corresponds to a specific electrical device, in this case, light bulbs. When a particular gesture is recognized by the software, it triggers the associated digital pin to toggle, enabling the control of the corresponding appliance.
The code and circuit implementation work harmoniously to create an interactive and intuitive system for users to control their home appliances through hand gestures. The project provides not only a touchless and convenient interface but also showcases the potential of integrating computer vision and microcontroller technology in real-world applications, offering a glimpse into the future of smart homes and interactive environments.

Source code:
1.	import cv2
2.	import mediapipe as mp
3.	from pyfirmata import Arduino
4.	from time import sleep
5.	 
6.	board = Arduino(COM3)
7.	# Arduino define pins
8.	digitalPins = [7, 6, 5, 4]
9.	 
10.	for pins in digitalPins:
11.	    board.digital[pins].write(1)
12.	 
13.	wCam, hCam = 1920, 1080
14.	cap = cv2.VideoCapture(0)
15.	 
16.	cap.set(3, wCam)
17.	cap.set(4, hCam)
18.	 
19.	cap.set(10, 250)  # brightness
20.	cap.set(11, 50)  # Contrast
21.	cap.set(12, 100)  # saturation
22.	 
23.	 
24.	class handDetector:
25.	    def __init__(
26.	        self, mode=False, maxHands=2, modelComplexity=1, detectionCon=0.75, trackCon=0.5
27.	    ):
28.	        self.mode = mode
29.	        self.maxHands = maxHands
30.	        self.modelComplex = modelComplexity
31.	        self.detectionCon = detectionCon
32.	        self.trackCon = trackCon
33.	 
34.	        self.mpHands = mp.solutions.hands
35.	        self.hands = self.mpHands.Hands(
36.	            self.mode,
37.	            self.maxHands,
38.	            self.modelComplex,
39.	            self.detectionCon,
40.	            self.trackCon,
41.	        )
42.	        self.mpDraw = mp.solutions.drawing_utils
43.	 
44.	    def findHands(self, img, draw=True):
45.	        imgRGB = cv2.cvtColor(img, cv2.COLOR_BGR2RGB)
46.	        self.results = self.hands.process(imgRGB)
47.	 
48.	        if self.results.multi_hand_landmarks:
49.	            for handLms in self.results.multi_hand_landmarks:
50.	                if draw:
51.	                    self.mpDraw.draw_landmarks(
52.	                        img, handLms, self.mpHands.HAND_CONNECTIONS
53.	                    )
54.	        return img
55.	 
56.	    def findPosition(self, img, handNo=0, draw=True):
57.	        lmlist = []
58.	        if self.results.multi_hand_landmarks:
59.	            myHand = self.results.multi_hand_landmarks[handNo]
60.	            for id, lm in enumerate(myHand.landmark):
61.	                h, w, c = img.shape
62.	                cx, cy = int(lm.x  w), int(lm.y  h)
63.	                lmlist.append([id, cx, cy])
64.	                if draw:
65.	                    cv2.circle(img, (cx, cy), 3, (178, 102, 255), cv2.FILLED)
66.	        return lmlist
67.	 
68.	 
69.	counter, counter1, counter2, counter3 = 0, 0, 0, 0
70.	flag, flag1, flag2, flag3 = 0, 0, 0, 0
71.	 
72.	detector = handDetector()
73.	while True:
74.	    success, img = cap.read()
75.	    img = detector.findHands(img)
76.	    lmList = detector.findPosition(img, draw=False)
77.	    y1, y2 = 15, 80
78.	    grey_color = (211, 219, 218)
79.	    if lmList:
80.	 
81.	        tip8_x, tip8_y = lmList[8][1], lmList[8][2]  # tracking tip of index finger
82.	        # button 1
83.	        if tip8_x > 25 and tip8_x < 65 and tip8_y > 28 and tip8_y < 62:
84.	            counter += 1
85.	            cv2.rectangle(img, (15, y1), (85, y2), (255, 255, 0), cv2.FILLED)
86.	            if counter == 1:
87.	                flag = not flag
88.	        else:
89.	            counter = 0
90.	            if flag:
91.	                board.digital[digitalPins[0]].write(0)
92.	                cv2.rectangle(img, (15, y1), (85, y2), (76, 138, 246), cv2.FILLED)
93.	                cv2.putText(
94.	                    img,
95.	                    BULB1,
96.	                    (25, 50),
97.	                    cv2.FONT_HERSHEY_PLAIN,
98.	                    1,
99.	                    (255, 255, 255),
100.	                    2,
101.	                )
102.	            else:
103.	                board.digital[digitalPins[0]].write(1)
104.	                cv2.rectangle(img, (15, y1), (85, y2), grey_color, cv2.FILLED)
105.	                cv2.putText(
106.	                    img,
107.	                    BULB1,
108.	                    (25, 50),
109.	                    cv2.FONT_HERSHEY_PLAIN,
110.	                    1,
111.	                    (255, 255, 255),
112.	                    2,
113.	                )
114.	 
115.	        # button 2
116.	        if tip8_x > 115 and tip8_x < 155 and tip8_y > 28 and tip8_y < 62:
117.	            counter1 += 1
118.	            cv2.rectangle(img, (175, y1), (105, y2), (255, 255, 0), cv2.FILLED)
119.	            if counter1 == 1:
120.	                flag1 = not flag1
121.	        else:
122.	            counter1 = 0
123.	            if flag1:
124.	                board.digital[digitalPins[1]].write(0)
125.	                cv2.rectangle(img, (175, y1), (105, y2), (77, 198, 254), cv2.FILLED)
126.	                cv2.putText(
127.	                    img,
128.	                    BULB2,
129.	                    (115, 50),
130.	                    cv2.FONT_HERSHEY_PLAIN,
131.	                    1,
132.	                    (255, 255, 255),
133.	                    2,
134.	                )
135.	            else:
136.	                board.digital[digitalPins[1]].write(1)
137.	                cv2.rectangle(img, (175, y1), (105, y2), grey_color, cv2.FILLED)
138.	                cv2.putText(
139.	                    img,
140.	                    BULB2,
141.	                    (115, 50),
142.	                    cv2.FONT_HERSHEY_PLAIN,
143.	                    1,
144.	                    (255, 255, 255),
145.	                    2,
146.	                )
147.	 
148.	        # button 3
149.	        if tip8_x > 200 and tip8_x < 245 and tip8_y > 28 and tip8_y < 62:
150.	            counter2 += 1
151.	            cv2.rectangle(img, (265, y1), (195, y2), (255, 255, 0), cv2.FILLED)
152.	            if counter2 == 1:
153.	                flag2 = not flag2
154.	        else:
155.	            counter2 = 0
156.	            if flag2:
157.	                board.digital[digitalPins[2]].write(0)
158.	                cv2.rectangle(img, (265, y1), (195, y2), (135, 186, 118), cv2.FILLED)
159.	                cv2.putText(
160.	                    img,
161.	                    BULB3,
162.	                    (205, 50),
163.	                    cv2.FONT_HERSHEY_PLAIN,
164.	                    1,
165.	                    (255, 255, 255),
166.	                    2,
167.	                )
168.	            else:
169.	                board.digital[digitalPins[2]].write(1)
170.	                cv2.rectangle(img, (265, y1), (195, y2), grey_color, cv2.FILLED)
171.	                cv2.putText(
172.	                    img,
173.	                    BULB3,
174.	                    (205, 50),
175.	                    cv2.FONT_HERSHEY_PLAIN,
176.	                    1,
177.	                    (255, 255, 255),
178.	                    2,
179.	                )
180.	 
181.	        # button 4
182.	        if tip8_x > 295 and tip8_x < 335 and tip8_y > 28 and tip8_y < 62:
183.	            counter3 += 1
184.	            cv2.rectangle(img, (355, y1), (285, y2), (255, 255, 0), cv2.FILLED)
185.	            if counter3 == 1:
186.	                flag3 = not flag3
187.	        else:
188.	            counter3 = 0
189.	            if flag3:
190.	                board.digital[digitalPins[3]].write(0)
191.	                cv2.rectangle(img, (355, y1), (285, y2), (127, 129, 29), cv2.FILLED)
192.	                cv2.putText(
193.	                    img,
194.	                    BULB4,
195.	                    (295, 50),
196.	                    cv2.FONT_HERSHEY_PLAIN,
197.	                    1,
198.	                    (255, 255, 255),
199.	                    2,
200.	                )
201.	            else:
202.	                board.digital[digitalPins[3]].write(1)
203.	                cv2.rectangle(img, (355, y1), (285, y2), grey_color, cv2.FILLED)
204.	                cv2.putText(
205.	                    img,
206.	                    BULB4,
207.	                    (295, 50),
208.	                    cv2.FONT_HERSHEY_PLAIN,
209.	                    1,
210.	                    (255, 255, 255),
211.	                    2,
212.	                )
213.	 
214.	        # Exit button
215.	        cv2.rectangle(img, (600, y1), (530, y2), (102, 102, 255), cv2.FILLED)
216.	        cv2.putText(
217.	            img, EXIT, (550, 50), cv2.FONT_HERSHEY_PLAIN, 1, (255, 255, 255), 2
218.	        )
219.	        # EXIT by index finger
220.	        cv2.circle(img, (tip8_x, tip8_y), 10, (255, 255, 51), cv2.FILLED)
221.	        if tip8_x > 550 and tip8_x < 590 and tip8_y > 28 and tip8_y < 62:
222.	            cv2.destroyAllWindows()
223.	            cap.release()
224.	            break
225.	 
226.	    cv2.imshow(Image, img)
227.	    cv2.waitKey(1)
228.	 


Brief code:
This Python code uses OpenCV, MediaPipe, and the PyFirmata library to create a computer vision-based interface for controlling digital pins on an Arduino board using hand gestures. Heres a brief summary of what the code does:

•	Import necessary libraries:
  	 - cv2: OpenCV for computer vision tasks.
  	 - mediapipe as mp: MediaPipe library for hand tracking.
  	 - Arduino and sleep from PyFirmata for communication with the Arduino board.
•	 Initialize the Arduino board and define the digital pins to control external devices (in this case, 4 digital pins).
•	Set up the camera for capturing video from the default camera (webcam). It configures settings such as resolution, brightness, contrast, and saturation.
•	Define a class `handDetector` for hand tracking and landmark detection using the MediaPipe Hands module. It allows for detecting hands in the video stream and finding their positions and landmarks.
•	 Initialize counters and flags for each button (BULB1, BULB2, BULB3, BULB4) and corresponding digital pins.
•	Create an instance of the `handDetector` class.
•	Enter an infinite loop for processing video frames from the webcam.
•	Inside the loop:
   	- Read a frame from the webcam.
   	- Use the `handDetector` to find and draw hand landmarks in the frame.
   	- Extract the position of the tip of the index finger (landmark 8).
- Implement logic to control four buttons (BULB1, BULB2, BULB3, BULB4) and an EXIT button using the hands position and gestures.
- Toggle the state of each button (and corresponding digital pin) based on the position of the index fingers tip.
- If the EXIT button is activated, close the video window and release the webcam, then exit the loop.
•	 Display the modified frame with drawn landmarks and buttons using `cv2.imshow()`.
•	 Wait for a key press with `cv2.waitKey(1)` to update the frame and handle user interactions.

The code essentially tracks hand movements and finger positions in real-time video, using those positions to control the state of digital pins on the Arduino board, thereby controlling external devices like light bulbs. It also provides an EXIT button for ending the program when a specific gesture is recognized.
Circuit Diagram:
 


Circuit explanation:
The provided code controls digital pins on an Arduino board based on hand gestures detected through a webcam using OpenCV and the Mediapipe library. The circuit for this project is relatively simple and involves connecting the Arduino board to some external components, specifically lightbulbs or LEDs, to demonstrate control. Heres a high-level explanation of the circuit:
Components:
Arduino Board (e.g., Arduino Uno): This is the central controller for the project. It receives commands from the Python code running on the computer and controls the digital pins accordingly.
Digital Pins (D7, D6, D5, D4): These pins on the Arduino board are used to control external devices, such as lightbulbs or LEDs. Each digital pin can be set to either HIGH (1) or LOW (0), allowing you to turn connected devices on and off.
Lightbulbs or LEDs: The project simulates controlling lightbulbs. You would connect these to the digital pins on the Arduino. When a digital pin is set to HIGH, the corresponding lightbulb or LED should light up, and when set to LOW, it should turn off.

Circuit Connections:
Arduino to Computer: The Arduino board should be connected to the computer via USB to establish a serial communication link. In the code, its set up with COM3, but you need to ensure the correct port is selected based on your computer configuration.

Digital Pins to Lightbulbs/LEDs: Connect each of the digital pins (D7, D6, D5, D4) on the Arduino board to the anodes (positive terminals) of the respective lightbulbs or LEDs. The cathodes (negative terminals) of the bulbs or LEDs should be connected to the ground (GND) pin on the Arduino to complete the circuit.

Operation:
The Python code running on the computer captures video from the webcam, processes it to detect hand gestures (specifically the position of the tip of the index finger), and uses this information to control the digital pins on the Arduino board.

When the tip of the index finger is positioned within a certain range on the webcam feed, the code sets the corresponding digital pin to HIGH, effectively turning on the connected lightbulb or LED.

If the finger is moved out of that range, the code sets the digital pin to LOW, turning off the lightbulb or LED.

The code also provides a visual interface on the webcam feed to show the status of the bulbs and allows the user to exit the application.

This project is essentially a simple demonstration of human-computer interaction, where hand gestures control the digital pins on an Arduino board, simulating the control of electrical devices. The actual lightbulbs or LEDs may be connected in parallel to the digital pins to turn them on and off based on the detected gestures.

Benefits:
The code you provided demonstrates a practical application of computer vision and gesture recognition using a webcam and an Arduino board. While this specific implementation is a simple example, similar technology can be useful in various real-life scenarios:

•	Home Automation: This code can be a foundation for creating a gesture-controlled home automation system. You can use hand gestures to control lights, fans, air conditioning, and other smart home devices without touching any physical switches or using a smartphone app.

•	Accessibility: Gesture recognition can greatly assist individuals with physical disabilities. People with limited mobility can control devices and interact with computers or communication systems using hand gestures, making technology more accessible and inclusive.

•	Public Kiosks: Gesture-controlled interfaces can be used in public places like museums, interactive displays, or information kiosks. Users can navigate through content or interact with the kiosk without physical contact, reducing the risk of disease transmission, especially in a post-pandemic world.

•	Virtual Reality (VR) and Gaming: Hand tracking and gesture recognition are integral to VR and gaming experiences. They allow users to interact with virtual objects and environments more intuitively and realistically.

•	Industrial Applications: In manufacturing and industrial settings, workers can control machinery or robotic systems using gestures, which can improve safety and efficiency.

•	Healthcare: Gesture control can be used in healthcare for hands-free control of medical equipment, patient monitoring, and telemedicine applications. Surgeons, for instance, can control digital screens during procedures without touching them.

•	Education: Gesture control can enhance educational technology. Teachers and students can use gestures to navigate content on interactive whiteboards or screens, creating a more engaging learning experience.

•	Entertainment and Events: Gesture-controlled lighting and special effects can be used in entertainment and live events, creating captivating experiences for audiences.

•	Security: Gesture recognition can be used for biometric security, enhancing access control systems and authentication processes.

•	Innovation and Prototyping: Developers can use gesture recognition as a basis for prototyping and experimenting with new ways of human-computer interaction, potentially leading to innovative applications in various fields.

While the provided code focuses on controlling digital pins for light bulbs, it’s important to understand that gesture recognition is a versatile technology with applications beyond this specific example. It has the potential to improve user experiences, increase accessibility, and streamline various processes in our daily lives. As technology continues to advance, we can expect more practical and innovative applications of gesture recognition in different industries and settings.

Conclusion: 
In summary, the Gesture-Controlled Home Automation System is a promising project that uses computer vision and an Arduino board to control household devices via hand gestures. While it’s a great start, theres room for expansion and improvement, including voice control and broader device compatibility. This technology has applications in various fields, from healthcare to entertainment, making it a versatile and innovative solution. It exemplifies the potential of gesture recognition in enhancing convenience and accessibility in our daily lives.
