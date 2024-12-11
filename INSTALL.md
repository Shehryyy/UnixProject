Buying Raspberry Pi 4:

Either go to www.amazon.ca or www.canakit.com to purchase a Canakit Raspberry Pi 4 kits. 

Choosing a Raspberry Pi 4 kit:
Any kit can work for this project, but because we’ll be storing a lot of pictures, try purchasing one that has at least +32GB memory storage. For ram it depends on how many tasks you’re planning to run, but for this project either 4 or 8 will be sufficient. Lastly, make sure the kit has a fan included to prevent it from overheating.  

 
Installing a Raspberry Pi 4 kit:

Inside the box you’ll see the following items:
-	Raspberry Pi 4 board (8GB RAM)
-	32GB pre-loaded MicroSD card with NOOBS
-	Premium High-Gloss case
-	Fan
-	USB-C power supply 
-	Micro HDMI to HDMI cable
-	USB-C PiSwitch
-	Heat sinks

For assembling the case I used this video:
https://youtu.be/Wb1YForDARU?si=3weOgPXA9XcIdbqb


Connecting to the Monitor:

1)	Make sure your case is fully assembled. 
2)	Connect the Micro HDMI to HDMI cable to one of the Micro HDMI ports on the Raspberry Pi (HDMI0 is recommended because it’s the primary).
3)	Plug the other end into your monitor.
4)	Plug your USB keyboard and USB mouse into the available USB ports on the Raspberry Pi (two USB 3.0 and two USB 2.0 ports).
5)	Connect the USB-C PiSwitch to the USB-C power port on the Raspberry Pi (this helps you easily turn on and off without unplugging it from the power supply). 
6)	Plug the CanaKit USB-C power supply into the PiSwitch power input port.
7)	Plug the power supply into an electrical outlet.
8)	Press the PiSwitch to turn on the Raspberry Pi (the raspberry Pi will begin by displaying the NOOBS boot menu on the screen since the SD card is pre-loaded with NOOBS).
9)	Choose Raspberry Pi OS and click install and then wait for the OS to install.
10)	 Once installed, select your preferences and click next.
11)	 Connect to your WI-FI network. 
12)	 Make sure it’s running with the latest software.
13)	 Create User
14)	 Finally, your Raspberry Pi should now be set up.

You can also use this video for a visual step by step guide on how to set it up:
https://youtu.be/2nR06HRpWoY?si=mAdSDBynrZCDES4-&t=1420


Installing Pir motion sensor and camera to the Raspberry Pi:

What is needed:

-	Raspberry Pi 4 model b (with a microSD card preloaded with Raspberry Pi OS)
-	Raspberry Pi Camera. 
-	PIR Motion Sensor which should have three pins: VCC, GND, and OUT
-	Jumper wires to connect the PIR sensor to the Raspberry Pi GPIO pins (female to female wire is recommended)

Plugging the camera to the model:

-	Make sure the Raspberry Pi is turned off.
-	Find the 2-lane MIPI CSI camera port. 
-	Lift the plastic latch of the CSI upward.
-	Attach the ribbon cable to the camera port and make sure the metal connectors on the ribbon cable are facing the HDMI port.
-	Push the latch back down to secure.

Making sure the camera is properly connected:

-	Turn on the raspberry pi
-	Connect the raspberry pi model to the laptop using usb-c to usb-c cable.
-	Open terminal and type “ssh <username>@<ip address of the raspberry pi>
-	Update the system by typing the following in the terminal:
"sudo apt update"
"sudo apt upgrade -y"
-	Open the Raspberry Pi configuration tool by typing: 
"sudo raspi-config"  
Then go to Interface Options -> I2C and enable it.
-	Reboot the raspberry pi by typing: 
"sudo reboot"
-	Use RealVNC Viewer to see the Raspberry pi OS visually

Testing the camera:

-	Install the libcamera tools by typing: 
"sudo apt update"
"sudo apt install -y libcamera-apps"
-	Now to test the camera we type:
"Libcamera-jpeg -o test.jpg"
-	This should save an image in the home directory

For the last 3 steps this video can be used as visual step by step: 
https://youtu.be/yhM1NhD-kGs?si=GXFxC2PB8wAONrHW

Installing the Python library:

-	Install the python library by typing:
"sudo apt-get install python3-picamera"


Connecting the PIR Motion Sensor and Camera:

-	Connect the VCC to 5V (Pin 2) on the Raspberry Pi via jumper wires
-	Connect GND to GND (Pin 6) on the Raspberry Pi
-	Connect OUT to GPIO17 (Pin 11)

Creating a new file to include the script:

-	Create a new file
"sudo nano project.py"
-	Include the following code:


import RPi.GPIO as GPIO
import time
import os
import subprocess

SENSOR_PIN = 17

GPIO.setwarnings(False)
GPIO.setmode(GPIO.BCM)
GPIO.setup(SENSOR_PIN, GPIO.IN, pull_up_down=GPIO.PUD_DOWN)

SAVE_FOLDER = "/home/Shaheryar/motion_pics/"

if not os.path.exists(SAVE_FOLDER):
    os.makedirs(SAVE_FOLDER)

def motion_detected():
    timestamp = time.strftime("%Y%m%d-%H%M%S")
    picture_filename = os.path.join(SAVE_FOLDER, f"motion_{timestamp}.jpeg")
    command = f"libcamera-jpeg -o {picture_filename}.jpeg"

    try:
        subprocess.run(command, shell=True, check=True)
        print(f"Picture saved as {picture_filename}")
    except subprocess.CalledProcessError as e:
        print(f"Error while taking picture: {e}")

try:
    print("Looking for motion. Press Ctrl+C to exit.")
    while True:
        if GPIO.input(SENSOR_PIN): 
            print("Motion Detected! Taking picture...")
            motion_detected()
            time.sleep(2) 
        else:
            print("No motion detected")
            time.sleep(2)
        
        time.sleep(0.5)  

except KeyboardInterrupt:
    print("\nExiting program...")

finally:
    GPIO.cleanup()  

 
-	Run the script to make sure it works by typing:
"Python3 project.py"
-	Image should now be saved at /home/pi/motion_pics

