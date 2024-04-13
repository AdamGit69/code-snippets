# code-snippets
Just a spot to put random stuff.

### RANDOM THING NO.1 ###########
First up is this very rough and quick guide / starting point for your HA to take a cctv snapshot when motion is detected, then have the Google Generative AI Conversation integration to describe the snapshot, and then send a notification with said snapshot and description.

This works on Iphone, unsure if the image will come through with the notification on Android. 

Anyway, I see lots of others asking about this so I thought I'd share my YAML I use as an easy way to do it, as well as creating the actual snapshot WITHOUT using Frigate or your camera to take snapshots. 

Your cameras (and phone notifications) will of course need to be already added in HA and they will need to support motion detection and HA must have an entity to be able to see that motion alarm status... If so, read on. 

First up install the integration from here:  https://www.home-assistant.io/integrations/google_generative_ai_conversation/

Once you have that installed, create a directory under your www folder called "snapshots".

Ensure your camera has motion detection turned on and that HA can see the alarm when motion is detected.

Then create a AUTOMATION named:  "Camera 1 - Snapshot on motion"  using the following code: https://github.com/AdamGit69/code-snippets/blob/main/cctv_ai_notifications_automation
Edit to suit, switching to visual editor will help you find your camera  and entity id's. This automation is to run the below script when HA receives the motion alarm from your camera.

 After that create a SCRIPT called: "Camera 1 - Snapshot, AI & Notification" with the following code: https://github.com/AdamGit69/code-snippets/blob/main/cctv_ai_notification_script
Again edit to suit, switching to visual editor will help you find your camera id. This script takes the snapshot, gets the Google AI to describe it and then send you the notification with what the AI sees and with snapshot image attached. You can run this manually to test. :)

And I think that's it, you should now get a notification describing the image when your camera detects motion and HA takes the snapshot. Hopefully it gives you an idea how to do, you can edit the scripts prompt part to give you better answers and you can duplicate these automations and scripts for as many cameras as you like.  Finally, please reply below and let me know if this works and if I missed any steps! :)

EDIT: I'll just add that I whipped this up and if your camera detects a lot of motion, you will get a lot of notifications! It works well in my carport or shed where there isn't much movement. I also have automations to turn off motion detection for 10 minutes and turn on the lights ect when I arrive home. Also, there is better ways to save the snapshots with date and time in the filename but this, again, was just whipped up to get it working. When I get things like this sorted, I'll update but it will give you all a starting point for now anyway! 🙂

######### RANDOM THING NO.1 UPDATE ##############################

Thanks to Didgeridrew over on the HA forums for helping me with the IF/Then statement, I have now uploaded a new version of the script! 
This one takes 3 snapshots 0.5 seconds apart, sends those 3 images google to compare and if google notices no significant motion, the notification WILL NOT get sent. 

This hopefully cuts down on false notifications or ones from trees ect moving! It also helps the AI notice actual movement of things like cars...
BE AWARE: If you change the google prompt, especially the "If you see no obvious causes of motion, reply with "Camera has detected motion however no obvious motion observed comparing snapshots". part, you will also have to change the IF statement part to suit. 

ALSO NOTE: this sends 3 images to google, so if you get a "Error generating content: 400 Request payload size exceeds the limit" error, if you manually run the script, your camera snapshots are too big, and you have to adjust it back to 2 images by removing the last snapshot action, the  0.5 second delay before it and then removing the reference to the 3rd image from under image_filename in the google ai service part.... 

Phew, I hope that all makes sense! 🙂 

Anyway here is the updated YAML for the script part:
https://github.com/AdamGit69/code-snippets/blob/main/cctv_ai_notification_script_UPDATE

![419374252_786554813318795_8918303800431855253_n](https://github.com/AdamGit69/code-snippets/assets/103038993/76224ff4-fa9f-48ea-94f2-455b11c2992d)


![418298922_385943610736011_1569243628285837196_n](https://github.com/AdamGit69/code-snippets/assets/103038993/c85c0e08-2939-40a8-8c03-0126ec7107d0)

######################### RANDOM THING No.2 #####################################

I recently purchased some cheap Lenovo / Tuya motion detectors to use with HA, ALARMO and a couple automations to turn on the lights, where speed isn't that important. This issue is though, these detectors need to be added to HA with the official Tuya / Smartlife addon (unless you flash them) and they report a "detected" state the entire time... 

So, I wrote a couple automations to fix this. The first, "motion_detector_reset_state_after_motion" does as it says on the box, 10 seconds after detecting motion, this will reset the sensor state to clear. The second automation simply runs the first Automation when HA first starts up. I have multiple sensors, so this on HA startup resets all my detectors to clear.

To install you must have the motion detectors installed in Tuya, have HACS and enable & install the python script "set_state.py".

1) Install your motion detector into the Tuya addon via the Tuya / Smart life app. I had to click add manually, then select wifi motion sensor. For ease of this, call them "Motion Sensor 1", "Motion Sensor 2" etc etc.

2) Edit your HA configuration.yaml and add the following line:

python_script:

3) Create a directory called "python_scripts" under your main HA install directory. ie: /homeassistant/python_scripts. Create a new file called "set_state.py" and copy & paste the contents of: 
https://github.com/AdamGit69/code-snippets/blob/main/motion_detector_set_state.py  - Save file and reboot HA.

4) Create a new automation called "Motion Sensor 1 - Reset State After Motion" and copy & paste the contents of:
https://github.com/AdamGit69/code-snippets/blob/main/motion_detector_reset_state_after_motion-AUTOMATION
Edit it to suit if you didn't name your detectors "Motion Sensor 1" etc earlier, otherwise you'll need to change the two entity_ids to point to your motion detector.  You will need to create one of these automations for each one of your detectors.

5) Create another new automation called "Motion Detector Reset All States On Startup" and copy & paste the contents of:
https://github.com/AdamGit69/code-snippets/blob/main/motion_detector_reset_all_states_on_startup-AUTOMATION
Edit it to suit, changing the entity_ids to point to your automation(s) you created in step 4.

And that's it, your motion detectors should now function as you'd expect..

More info on the set_state.py python script can be found here:
https://github.com/xannor/hass_py_set_state

Happy HA'ing 🙂

##### Bonus: ###########
Here is the automation I use to turn on my lights when motion is detected by one of these devices. This is NOT that fast but it works good enough for places like my doorway, carport or shed. It can take a couple seconds to turn on once motion is detected, so I wouldn't use it on a staircase for example! lol 

Anyway, when motion is detected and is sent to HA, this automation checks first that the light is not already turned on (ie with the switch or whatever), then it turns on the light and waits until no motion is detected for 30 seconds and then turns them off again. If the light was already turned on this will not turn it off. It will only turn the light off again if this automation turned it on. The 30 second time along with the 10 seconds in the above automations means if no motion is detected in roughly 40 seconds, the lights will turn off... If you want to make the light go off quicker edit this time but I suggest a minimum of 30 seconds or you could end up with a disco... 🙂

There is a link below to the YAML, I suggest creating a new automation, copy & paste the contents, and then switch to visual editor to find all your device and entity ids. 

You will need to edit (in visual as I said is easier):

The when section:
 Edit to the Entity ID of your motion detector and the "to" should be set to detected

The if section:
 Edit the device id to the light you want to check is off (which will be the light we turn on with motion below).
 The condition should be XYZ light is off

The do section:
Change the first part, the "light turn on" dropdown thingy, to the target device of your light you want motion to turn on.
The wait for 1 trigger part, set your motion sensor device id, the trigger should be XZY STOPPED detecting motion.
SET THE DURATION TO 30 SECONDS.
Finally change the "light turn off" part, to the target device of your light.

Automation YAML can be found here:
https://github.com/AdamGit69/code-snippets/blob/main/motion_detector_turn_on_light_on_motion-AUTOMATION

