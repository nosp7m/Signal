# TextSecure without GCM

Thanks to the websocket polling Whispersystems have added to TextSecure to improve reliability and (maybe) start moving away from GCM, it is now possible to use TextSecure without GCM with absolutely minuscule modifications (i.e. commenting out a few lines of code). So that you do not have to invest the time that I did, I have uploaded my changes here along with a short guide.

## Building TextSecure

I will assume here that you already have a working setup to build (and install) TextSecure. If not, follow [their instructions](https://github.com/WhisperSystems/TextSecure/wiki/How-to-build-TextSecure-from-the-sources).

## Getting your GCM ID

For some reason, when I first tested the modified app, the TextSecure server did not deliver any messages to me. To fix that, I registered a GCM ID with it. The idea is to run a modified app (see my comments in src/org/thoughtcrime/securesms/service/RegistrationService.java) on a device which does support GCM to get a GCM ID and log it out to the console. Handily, you can install the Google APIs package on your android sdk tools (which you should already have for the build), which (as the name implies) allows apps to access Google APIs, including GCM.

1. Modify TextSecure and build and sign
 - You will need to modify the file src/org/thoughtcrime/securesms/service/RegistrationService.java. I have left to comments in there so you know what to uncomment.

2. Create an AVD (see also https://developer.android.com/tools/devices/managing-avds-cmdline.html):
 - find the target id with (in the tools/ directory) "./android list targets"
 - create a new avd with "./android create avd -n whisper -t <target id>"

3. Run the AVD (see also https://developer.android.com/tools/devices/emulator.html)
 - "./emulator -avd whisper"
 - wait for it to start up

4. Install TextSecure
 - from the platform-tools/ directory, run "./adb install <path to apk>"

5. Run TextSecure and get your ID
 - On the emulator, start TextSecure and begin the registration process. You can use your own phone number.
 - Connect to the emulator using telnet "telnet localhost 5554" (usually the port number will be 5554, if not, the port number ist displayed in the window title of the emulator).
 - From inside telnet, send an sms you received on your phone to the emulator: "sms send <TextSecure phone number> <sms verification message (just type it in)>"
 - Alternatively, wait the two minutes and then receive the call to manually enter the code.
 - Look at the logcat output for your ID: "./adb logcat RegistrationService:W \*:S". (also see https://developer.android.com/tools/debugging/debugging-log.html)
   Some line will say "GCM ID: " and then a string of characters. This is your ID!
 - You can now close the emulator.

## Modifying the app

Actually, all the lines you would need to comment out are already commented out by me, so there is not much to do here. In RegistrationService.java you can now comment the lines you uncommented for the emulator run out again and uncomment the other two below. Also paste in your GCM ID. Now you are ready to build and use TextSecure!

## Updating

As you have installed the app manually, you will also need to do the updates manually. Of course, you do not need a new GCM ID for each update, so it should be as simple as fetching the TextSecure git repo, merging, and then rebuilding, resigning and reinstalling the app. Android will recognize that you are installing it again and will keep your data.

Good luck!
 

# Legal things
## Cryptography Notice

This distribution includes cryptographic software. The country in which you currently reside may have restrictions on the import, possession, use, and/or re-export to another country, of encryption software.
BEFORE using any encryption software, please check your country's laws, regulations and policies concerning the import, possession, or use, and re-export of encryption software, to see if this is permitted.
See <http://www.wassenaar.org/> for more information.

The U.S. Government Department of Commerce, Bureau of Industry and Security (BIS), has classified this software as Export Commodity Control Number (ECCN) 5D002.C.1, which includes information security software using or performing cryptographic functions with asymmetric algorithms.
The form and manner of this distribution makes it eligible for export under the License Exception ENC Technology Software Unrestricted (TSU) exception (see the BIS Export Administration Regulations, Section 740.13) for both object code and source code.

## License

Copyright 2011 Whisper Systems

Copyright 2013-2014 Open Whisper Systems

Licensed under the GPLv3: http://www.gnu.org/licenses/gpl-3.0.html
