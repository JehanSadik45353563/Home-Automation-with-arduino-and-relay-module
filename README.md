**Home Automation with Arduino and relay module**

**Project Overview:**
The Gesture-Controlled Home Automation System combines computer vision, Arduino, and home automation to enable users to control household appliances—specifically light bulbs—using hand gestures captured via a webcam. This project aims to enhance user convenience and showcase the practical application of gesture recognition technology in everyday life.

**Components Used:**
- **Arduino Board:** Acts as the central controller, facilitating communication with the relay module and handling gesture logic.
- **Relay Module:** Controls high-voltage electrical devices (e.g., light bulbs) based on signals received from the Arduino board.
- **Webcam:** Captures live video frames used for hand gesture detection via computer vision.
- **Computer:** Runs the Python script that processes video frames, detects gestures, and sends commands to the Arduino.
- **Electrical Devices:** Demonstrates control of light bulbs; can be expanded for other appliances.

**Features:**
- **Computer Vision:** Utilizes OpenCV for capturing and processing video frames, and MediaPipe for hand tracking and gesture recognition.
- **Arduino Integration:** Arduino Uno manages digital pins to control relay modules connected to electrical appliances.
- **User Interface:** Displays real-time feedback on gesture recognition status and appliance control through graphical overlays on the webcam feed.
- **Accessibility:** Enables touchless interaction with household appliances, enhancing accessibility and user experience.

**Usage:**
1. **Setup:** Connect Arduino to the computer via USB. Ensure relay modules are correctly wired to control appliances.
2. **Execution:** Run the Python script on the computer. Adjust webcam settings for optimal gesture detection.
3. **Interaction:** Perform predefined hand gestures in front of the webcam to toggle the state of connected appliances (e.g., turn light bulbs on/off).
4. **Termination:** Exit the application using a specified gesture or the provided exit button.

**Applications:**
- **Smart Homes:** Offers intuitive control of various appliances without physical contact, enhancing convenience and efficiency.
- **Accessibility Tools:** Supports users with disabilities by providing an alternative, gesture-based interface for operating devices.
- **Public Kiosks:** Enables touchless interaction in public spaces, reducing contact and improving hygiene.
- **Educational Demonstrations:** Demonstrates the integration of computer vision and IoT in real-world applications, suitable for educational purposes.

**Conclusion:**
The Gesture-Controlled Home Automation System exemplifies the integration of modern technologies to create innovative solutions for everyday challenges. By leveraging computer vision and Arduino microcontrollers, this project not only showcases the potential of gesture recognition but also demonstrates practical applications in enhancing home automation and accessibility.
