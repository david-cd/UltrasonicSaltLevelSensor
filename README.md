# UltrasonicSaltLevelSensor
Ultrasonic Salt Level Sensor for Water Softener using ESPHome, D1 Mini, HC-SR04, and Home Assistant

This project provides an overview of how to make a salt level sensor for a water softener and how to integrate it into Home Assistant. It uses a D1 Mini running ESPHome connected to an ultrasonic sensor (HC-SR04) and an LED for visual notification that salt level is low. 

**Pre-requisites:**

This project assumes you have a functioning instance of Home Assistant running on your network (https://www.home-assistant.io/) and know how to solder.

This video by DrZzs provides the starting point for this project: https://youtu.be/0zUp7Dia4l4

**Materials required:**

- D1 Mini (or other ESP-based device)
- Ultrasonic Sensor (HC-SR04)
- LED (for status notifications)
- Electronics Wires
- Soldering iron

**Optional:**

- 3D printer to print enclosure for components
- Hot Glue Gun

**STEP 1: Start with installing ESPHome on your D1 Mini** (https://esphome.io/)
- Connect your D1 Mini to your computer with a micro USB cable.
- Go to the Getting Started with ESPHome and Home Assistant page: https://esphome.io/guides/getting_started_hassio.html
- Install ESPHome Dashboard following the instructions. This installs an add-on in Home Assistant Supervisor which you can use to manage your ESPHome devices. 
- Make sure you have the correct driver for your board (see the drivers note on the getting started page)
- Once the ESPHome Dashboard add-on is installed you can access it by going to Home Assistant > Supervisor > Dashboard > ESPHome
- Click Start and wait a bit for it to start up. Once it is started, Click on Open Web UI
![image](https://user-images.githubusercontent.com/29218631/124334609-7dd46100-db4c-11eb-8bbe-5b3bc7039b55.png)
- Click the green + in the bottom right to add a new device
![image](https://user-images.githubusercontent.com/29218631/124334650-99d80280-db4c-11eb-85ac-39041851397a.png)
- Input your name (salt-level), Wi-Fi SSID, and Wi-Fi password
- When asked for the ESP device select "ESP8266" or use "Pick specific board" and select "Wemos D1 and Wemos D1 mini"
- Your device configuration is created!
- Click INSTALL, Plug into this computer (NOTE you need to be connected to Home Assistant using HTTPS in your URL, not HTTP for initial configuration to work through the browser), and follow the prompts.
  - If you aren't using HTTPS or are having trouble you can use esphome-flasher (https://github.com/esphome/esphome-flasher/releases). Download the appropriate file for your file system and run it. You should see a screen that looks like this:

![image](https://user-images.githubusercontent.com/29218631/124336221-0a355280-db52-11eb-8f8b-8b1fa8c96302.png)

  - Under the Serial port dropdown, select the COM port for your device. There should only be one, but if you see multiple then unplug your device, refresh, and see which port disappeared. Plug your device back in and choose the port that just showed up. 
  ![image](https://user-images.githubusercontent.com/29218631/124336337-71eb9d80-db52-11eb-91f1-34f3e284bdb5.png)
  - In the ESPHome Dashboard, click INSTALL and select Manual download. Let the software compile the file (a minute or so) and save the file to your desktop or downloads folder for easy access.
  - In esphome-flasher, click BROWSE next to the Firmware field and select the .bin file that was just downloaded
  - Verify that you have the correct port and file selected, then click Flash ESP
  ![image](https://user-images.githubusercontent.com/29218631/124336451-ef171280-db52-11eb-80bb-9dbe3e079d35.png)

  - It should finish uploading in about 30 seconds and connected to your Wi-Fi. You can now power the device from a USB power adapter and update over the air (OTA) moving forward.
- You should now see your device in the ESPHome Dashboard with a green line indicating it is online. The default configuration file doesn't do anything though, so in Step 2 we will add the features we want.
![image](https://user-images.githubusercontent.com/29218631/124336566-58972100-db53-11eb-9d7f-4eed84cbdab7.png)

**STEP 2:** Now let's modify the configuration to incorporate the items we want:
- Click EDIT on the new device you just created.

![image](https://user-images.githubusercontent.com/29218631/124334559-4e255900-db4c-11eb-89cf-d6a65d7bffef.png)
- Leave all of the text in the existing file and add the following at the end (see the salt-level.yaml file for full configuration file text):
  - If you don't want an LED you can stop at line 31. Lines 32 and beyond are for incorporating an LED that turns on when salt level is low.
![image](https://user-images.githubusercontent.com/29218631/124334829-34384600-db4d-11eb-9e60-d73d680f2678.png)

**STEP 2A (Ultrasonic Sensor)**
Let's take a look at what each of these sections does:

![image](https://user-images.githubusercontent.com/29218631/124334920-73ff2d80-db4d-11eb-9777-111b95e25bd4.png)
- ![image](https://user-images.githubusercontent.com/29218631/124340580-0a8e1780-db6b-11eb-8f48-21c18566ce88.png) indicates that you are adding a sensor to the device/configuration. This sensor will also be pulled into Home Assistant when the device comes online
- ![image](https://user-images.githubusercontent.com/29218631/124340584-15e14300-db6b-11eb-9146-d3ffce9815fb.png) tells ESPHome what type of sensor is being used. See https://esphome.io/components/sensor/ultrasonic.html for more detail on ultrasonic distance sensors and integration into ESPHome.
- ![image](https://user-images.githubusercontent.com/29218631/124340591-20034180-db6b-11eb-8c70-08b70b2b8d89.png) says that I am connecting the trigger pin from my HC-SR04 to the D2 pin on the D1 Mini board.
- ![image](https://user-images.githubusercontent.com/29218631/124340599-27c2e600-db6b-11eb-91ae-0e71b762c67c.png) says that I am connecting the echo pin from my HCSR04 to the D1 pin on the D1 Mini board.
- ![image](https://user-images.githubusercontent.com/29218631/124340607-34473e80-db6b-11eb-8f7f-5be642fba909.png) is the name assigned to the sensor. This is what you will see in Home Assistant when the distance sensor is added.
- ![image](https://user-images.githubusercontent.com/29218631/124340613-3d381000-db6b-11eb-9b5b-46ee5e3276d3.png) says that the sensor will update the value every 12 hours. During configuration and testing you will likely want to set this somewhere from 1s to 30s so you can make sure everything is working. Once you have everything in its place and are finished with the project you can change the interval to whatever makes sense for you. I think 12 hours is fine when looking at salt levels in a water softener.

If you only wanted the sensor to come into Home Assistant and didn't want a visual indicator/LED to come on when salt level is low then you are DONE with configuration. Move on to Step 3.

**STEP 2B (LED Configuration)**

OPTIONAL: If you want to add an LED to your device as a visual indicator that the salt levels are low you can include the code below to trigger actions (see https://esphome.io/guides/automations.html for more details on Automations and Templates in ESPHome).

![image](https://user-images.githubusercontent.com/29218631/124335154-45ce1d80-db4e-11eb-88e2-a447d08ba9a9.png)

Starting on line 32:
- ![image](https://user-images.githubusercontent.com/29218631/124335443-1ff54880-db4f-11eb-99ff-9f10c80d4ed4.png) is an action that will trigger based on sensor value
- ![image](https://user-images.githubusercontent.com/29218631/124335483-46b37f00-db4f-11eb-8a5a-ca3d7ee23558.png) indicates that if the sensor reads above 0.40m (salt is low) then it will turn on the light (defined later) using the "Slow Pulse" effect (defined later). You will need to do some testing to determine what 'full' and 'low' are for your specific setup. For mine, mostly full was around 0.25m and no salt was around 0.50m.
- ![image](https://user-images.githubusercontent.com/29218631/124335510-6185f380-db4f-11eb-842a-3f244256c4ea.png) indicates that if the sensor reads a value below 0.40m (salt is filled) then turn the LED light off.

Now we will tell ESPHome that we have a light and provide some information about the light.
![image](https://user-images.githubusercontent.com/29218631/124335610-b0338d80-db4f-11eb-85ef-9064c47dab2f.png)
- ![image](https://user-images.githubusercontent.com/29218631/124335625-baee2280-db4f-11eb-9744-659983b67e6b.png) tells ESPHome that we have a light and the type of light is monochromatic (single color). You can also have a light that is binary (on/off), RGB, and others. See https://esphome.io/index.html#light-components for more detail on lights.
- ![image](https://user-images.githubusercontent.com/29218631/124335680-f12ba200-db4f-11eb-8621-52f2eac4413b.png) the name of the light is defined (again, this will be pulled into Home Assistant), output name is referenced (defined below), and id for this light is defined. All of these can be named anything you want. Just remember what you called it so you can reference it in the future.
- ![image](https://user-images.githubusercontent.com/29218631/124335778-57182980-db50-11eb-8053-952613ca2db2.png) defines an effect that can be used on the light. Pulse will slowly turn the LED on and off. I called it "Slow Pulse" but any name can be used. It will take 1s to transition to on or off and stay on or off for 1s each. Adjust these values to whatever you prefer. See light effects page for more details and types of effects available https://esphome.io/components/light/index.html#light-effects. NOTE: If you just want the LED to turn on (no blinking) when the level is low then you can skip the "effects:" section (lines 47-51).
- ![image](https://user-images.githubusercontent.com/29218631/124335892-c1c96500-db50-11eb-8104-5e13ee1fb7c2.png) defines the Output Component. It tells ESPHome where the LED is connected on the board (pin D3), what type of light it is (esp8266_pwm - pulse width modulation i.e. can be dimmed), and defines an ID (set this to whatever you want, but it MUST match the output from the previous section on line 45). 
- That's it, we're done with the configuration file. Click SAVE in the bottom right corner and close. 
- Make sure your device is powered on and online (green line above it) in ESPHome. Click VALIDATE and make sure you see "Configuration is valid!" at the top of the window that pops up, then if everything looks good click INSTALL in the lower right corner, select Wirelessly, and you should see information indicating the upload was successful (see screenshot below).
![image](https://user-images.githubusercontent.com/29218631/124340714-fc8cc680-db6b-11eb-9d6c-ac0e08e1912b.png)
![image](https://user-images.githubusercontent.com/29218631/124336671-b75c9a80-db53-11eb-8d2c-4ac7a380bd25.png)
![image](https://user-images.githubusercontent.com/29218631/124336708-df4bfe00-db53-11eb-80d9-e5fa4fb7f2fb.png)

- The configuration file is all set and now we will handle the hardware.

**STEP 3: Hardware**
- Unplug the D1 Mini and get your components together. You'll need the D1 mini, an LED (yellow is a nice option), an ultrasonic sensor (HC-SR04), and some wires. You'll also need your soldering iron.
- Look at your water softener salt tank and figure out how you want to mount your electronics. The ultrasonic sensor will be poking through the top of the lid through 2 holes that are drilled. The board and LED can be placed right next to the ultrasonic sensor or connected using a longer wire and placed somewhere else.
- Cut wires to the appropriate length for your component placement. You'll need 4 wires to connect to the ultrasonic sensor and 2 wires to connect to the LED. 
- Solder the wires to the D1 Mini, ultrasonic sensor, and LED following the wiring diagram below. Note that if you prefer to use one of the other pins on the D1 Mini you can choose any you want - you will modify the configuration file we just uploaded to address your pins
Wiring Diagram:
![image](https://user-images.githubusercontent.com/29218631/124337344-f2140200-db56-11eb-98b7-a866c43a8a7e.png)
  - 5V to VCC on ultrasonic sensor
  - G to the negative side of the LED (if you connect to the wrong side it won't turn on, swap the wires if you have this problem)
  - G to Gnd on ultrasonic sensor
  - D3 to positive side of LED (note, no resistor is needed since the D1 Mini outputs 3.3V on pin D3. **Other boards may output 5V**. If you are using another board make sure you check voltage before connecting the LED or it might burn out/pop.
  - D2 to Trig on ultrasonic sensor
  - D1 to Echo on ultrasonic sensor

- Plug your D1 Mini into the wall and see if everything works. You should be able to point the ultrasonic sensor across the room (with no obstructions) and the LED should turn on and start blinking. Point it face down on a table or face it towards a wall that is close and the LED should turn off. 
- Check your Home Assistant notifications (bottom left of Home Assistant and you should have "New devices discovered" notification
![image](https://user-images.githubusercontent.com/29218631/124340437-29d87500-db6a-11eb-91be-b2fd98784ba8.png)
- Click "Check it out" and you should see your salt-level ESPHome device. Click CONFIGURE and then SUBMIT
![image](https://user-images.githubusercontent.com/29218631/124340454-52f90580-db6a-11eb-8512-f608d765fd1c.png)
![image](https://user-images.githubusercontent.com/29218631/124340456-555b5f80-db6a-11eb-8967-8ceac2f77af4.png)
- Then select the Area it is in (if you want, you can always change this later) and click FINISH
![image](https://user-images.githubusercontent.com/29218631/124340472-660bd580-db6a-11eb-84a2-15d5ee3f905b.png)
- In Home Assistant go to Devices and find the device you just added. You should be seeing a Salt Level reading and a light switch. Clicking the light switch should turn your LED on and off. Moving the ultrasonic sensor should change the reading (keep in mind it only updates as frequently as the update interval you set earlier! You can update this any time by updating the config file and installing wirelessly again).
![image](https://user-images.githubusercontent.com/29218631/124340483-715f0100-db6a-11eb-8728-1d7a9151a9c9.png)
- As long as everything is working, you’re all set with the hardware. Now it’s time to install it! 

**STEP 4 (Installation)**
- You have some planning to do and if you have a 3D printer, some printing for an enclosure if you want. I attached an enclosure for the ultrasonic sensor in the files section. This is only for the ultrasonic sensor as I currently have the other electronics mounted separately. 
- Drill 2 holes in your water softener salt tank lid using the ultrasonic sensor as a template – these holes should allow the sensor to point straight down (or as close to vertical as possible). The holes need to be about 16mm (5/8” is a little small but might work. 11/16” is a little large but does work. A step drill bit is a great option to use).
- Hot glue or place the ultrasonic sensor in the lid holes. Mount the D1 Mini and LED where you want them, then plug it in.
- You’ll get a reading in Home Assistant and you can use that to determine salt level. As I mentioned, low on mine is about 0.40m and full is around 0.25m. This will depend on your placement of the sensor and your specific salt tank. Based on your readings you can go back and adjust the configuration file to turn the LED on at whatever height you want (on line 33 and line 38 – use the same number for both above and below unless you want different on/off triggers).
- Once you have everything set up adjust the update interval (line 31) to somewhere around 3h to 24h, whatever you think makes sense. No need to add a bunch of data if the salt level isn’t changing much.


You're DONE! Let me know if you have any issues or suggestions.

*************ADD PHOTOS FOR HARDWARE SOLDERING AND INSTALLATION ONTO WATER SOFTENER********************
