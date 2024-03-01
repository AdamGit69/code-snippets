# code-snippets
Just a spot to put ramdom stuff.

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
