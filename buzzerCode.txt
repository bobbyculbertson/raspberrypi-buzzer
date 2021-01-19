#!/usr/bin/python
import RPi.GPIO as GPIO
import time
GPIO.setmode(GPIO.BOARD)
GPIO.setwarnings(False)
#Create Button and LED Variables
button1 = 12
button2 = 7
led1 = 13
led2 = 11
global buttonPressed
buttonPressed = 0
#Initialize Channel Setups
GPIO.setup(button1, GPIO.IN, pull_up_down=GPIO.PUD_UP)
GPIO.setup(button2, GPIO.IN, pull_up_down=GPIO.PUD_UP)
GPIO.setup(led1, GPIO.OUT)
GPIO.setup(led2, GPIO.OUT)
#Initialize Channel Detection
GPIO.add_event_detect(button1, GPIO.FALLING)
GPIO.add_event_detect(button2, GPIO.FALLING)
#Flash LED lights to Signal Program is Running
#This is here because I run this program on Pi Startup. I wanted a visual with the lights to let me know everything is ready to go
GPIO.output(led1,True)
GPIO.output(led2, True)
time.sleep(.5)
GPIO.output(led2,False)
time.sleep(.25)
GPIO.output(led2,True)
GPIO.output(led1,False)
time.sleep(.25)
GPIO.output(led2,False)
#Initialize LED Light Channels Values
GPIO.output(led1,False)
GPIO.output(led2,False)
#Resets the Button Channels to receive input and Resets LED to OFF
def reset(led_channel):
        GPIO.add_event_detect(button1, GPIO.FALLING)
        GPIO.add_event_detect(button2, GPIO.FALLING)
        GPIO.output(led_channel,False)

#Reset not requiring led
def resetDetect():
        global buttonPressed
        buttonPressed = 0
        GPIO.add_event_detect(button1, GPIO.FALLING)
        GPIO.add_event_detect(button2, GPIO.FALLING)

#Debounce Function
def bounce(button):
        global buttonPressed
        buttonPressed = 0
        print "In Bounce"
        time.sleep(.01)
        if(GPIO.input(button)==0):
                print "Button Still pushed is %s" % button
                buttonPressed = button




#When a button is pushed, it first cancels all input detection
 #Next, it calls this function to display results, pause for 2 seconds, then call
 #Reset function to reset detection
def startOver(channel,led_channel):
        global buttonPressed
        buttonPressed = 0
        if channel == 12:
                button = 1
        else:
                button = 2
        print "BUTTON %s HAS BEEN PRESSED" % (button)
        GPIO.output(led_channel, True)
        print "INPUTS LOCKED"
        time.sleep(2)
        reset(led_channel)

        print "SYSTEM RESET - WAITING FOR INPUT"
try:

	 while True:
        #Button 1 Detection
                if GPIO.event_detected(button1):
                        bounce(button1)
GPIO.remove_event_detect(button1)
                        GPIO.remove_event_detect(button2)
                        print "Button pressed variable is %s" % buttonPressed
                        if(buttonPressed == button1):
                                startOver(button1, led1)
                        else:
                                buttonPressed = 0
                                resetDetect()
        #Button 2 Detection
                if GPIO.event_detected(button2):
		bounce(button2)
                        GPIO.remove_event_detect(button2)
                        GPIO.remove_event_detect(button1)
                        print "Button pressed variable is %s" % buttonPressed
                        if(buttonPressed == button2):
                                startOver(button2, led2)
                        else:
                                buttonPressed = 0
                                resetDetect()
except KeyboardInterrupt:
        GPIO.cleanup()
        print "  GPIO Pins Cleaned"


