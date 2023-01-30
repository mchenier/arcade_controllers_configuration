# Arcade controller setup 2 players, 2 lightguns (wiimote) and 1 wheel

Having an arcade with multiple kind of game, emulators and controllers is not easy to configure. I was able to get a good configuration but since it requires a lot of tools and configuration I gathered all the information here so I can share what I have learned and remember what I did.
My goal was having two arcade player configure as a Xbox controller so I can play MAME and Windows games. Also two lightguns (using WiiMote) to play lightgun games on MAME and m2emulator (VirtuaCop) and also a pluggable [Xbox360 controller with a 3d printed wheel](https://www.thingiverse.com/thing:3049220) so I can play Sega Rally and Daytona USA for example.
Here is a list of thing you need to install and configure to get this setup working.


## XOutput
https://github.com/csutorasa/XOutput
Convert your DirectInput controller to XOutput (Xbox controller)

> Create 2 controllers for the main arcade controllers.  Map controller
> like you want but here is my choice.

#####  Arcade controller to Xbox controller mapping
> Arcade Stick = D-Pad
> Buttons layout:
> 1 2 3   =   RB Y B
> 4 5 6	=   LB X A
> Player1 Start: Start
> Player1 Coin: Back

## DevCon
https://github.com/Drawbackz/DevCon-Installer
Use for HidGuardian.
> Install in D:\Arcade\hg\devcon

## HidGuardian
https://github.com/ViGEm/HidGuardian
https://vigem.org/projects/HidGuardian/HidGuardian-Gen1-Installation/
Prevent double input that could occured in certain game that use DirectInput and XInput
That project is discontinued and now is know as HidHide but HidGuardian works for me so I keep it.
What I did:

> Extract the HidGuardian driver to D:\Arcade\hg
> Execute those command:

    D:\Arcade\hg\devcon\x64\devcon.exe install
    D:\Arcade\hg\x64\HidGuardian.inf Root\HidGuardian
    D:\Arcade\hg\devcon\x64\devcon.exe classfilter HIDClass upper
     -HidGuardian
   
## vJoy
https://github.com/shauleiz/vJoy/releases

> Install vJoy
> Open "Configure vJoy" make sure you have 2 vJoyDevice on tab 1 and 2.
> Each device should be 8 buttons, POV Continuous 0 and uncheck Rz, Slider, Dial/Slider2 and Enable effects
> Make sure Enable vJoy is checked

## Lichtknarre
https://geekonarium.de/en/lichtknarre-lightgun/
This tool deserve more attention. It is really simple to use and calibrate and make the Wiimote lightgun concept really work. You need a Bluetooth dongle (see the compatible one from lichtknarre). I use a Anker one and I think it recommend a TpLink. 

> Follow the instruction to get the lightgun working and make sure to
> use vJoy for P1 and P2

## devreorder
https://github.com/briankendall/devreorder
Since windows has a random way of choosing the player number associated with the controller with need that utility that will predetermine the player number on the controllers. So that the lightgun and arcade controller don't get mixed up.
You can install the devreorder for each emulator and program but I suggest intalling it system wide (see [link](https://github.com/briankendall/devreorder)). I had some problem with m2emulator since it was 32bit and my system is 64bit but for those I just copy the dinput.dll 32bit and devreorder.ini directly in there folder.
Here is my .ini file as a guideline but you will have to find out your controllers ID

    [order]
    ; Arcade Controller 1
    {f7c9c1a0-4170-11ec-8002-444553540000}
    ; Arcade Controller 2
    {2c298070-4171-11ec-8001-444553540000}
    ; WiiGun1
    {a017c0e0-8c35-11ed-8005-444553540000}
    ; WiiGun2
    {e251fde0-8c2b-11ed-8002-444553540000}
    
    [hidden]
    ;"Nintendo RVL-CNT-01": 
    {a5d6eee0-85fd-11ed-800f-444553540000}
    ;"Nintendo RVL-CNT-01": 
    {b5d379c0-86a3-11ed-8011-444553540000}
    
    [visible]
    
    [ignored processes]

Arcade Controller are the number found in the XOutput interface. Mine are called USB Joystick in XOutput. There is no way to copy them so write them down.
The WiiGun are the vJoy controller ID there is multiple way to get those but they are available in XOutput.
For the hidden section I have added the Wiimote themself so they don't enter in conflict.
To get the ID you can try using the DeviceLister that comes with devreorder or use [GamepadTool.win.latest](https://www.generalarcade.com/gamepadtool/)


## GamepadTool.win.latest
https://www.generalarcade.com/gamepadtool/
Check the controller available at the moment. Use to test your devreorder settings or find controller ID.

## DemulShooter
https://github.com/argonlefou/DemulShooter/releases
Im using this tool for m2emulator but there is support for more if you want to look into that.

> Install DemulShooter in the folder m2Emulator for easy access.
> Configure "P1 Configuration" by selecting the vJoy controller that
> correspond to lightgun 1 and do the same for "P2 Configuration" for
> lightgun 2. Also choose the buttons for trigger etc...

> Go in "m2Emulator" and install the crosshair script. 

The configuration here are use to change the crosshair in m2emulator. 
So remember those keys so you can try them in game.

# Emulator setup
At this point you should have all controller setup so we can configure emulators/games to work with your setup.

## m2Emulator

> Enable Xbox 360 support Run Configurator.exe and check the "Use Xbox
> 360 pad" box. Save. Exit.

For m2Emulator we need DemulShooter. To enable a game with that application I use a batch script.

> Create a file with extension .bat in you m2Emulator folder with this
> inside.

    DemulShooter.exe -target=model2 -rom=%1

Here %1 is a parameter that we will pass to this script. For example for VirtuaCop the word vcop would have to be passed (DemulShooter.exe -target=model2 -rom=vcop).  I use that script like this because I use LaunchBox and I can simply pass the name of the game so I don't have to make a .bat file for every game. But I have to configure each game manually by adding an Additional Apps with the name of the game in parameters and I check the Run before Main application.
Once that command is run you just launch the game in m2Emultaor and the lightgun should work. 
Note that the Coin and Start button have to be configure for each game inside the m2Emulator.

### Wheel
For the Wheel at the moment I just use an old Xbox360 controller that I plug in a front usb that I made available. Since it creates a XInput after the two main controller I don't have to include it anywhere in the configuration. I only have to setup the control in the m2Emulator per game basis.


## MAME
For MAME I didn't succeed using devreorder but the good news is that MAME as it's own thing for that called [Stable Controller Id](https://docs.mamedev.org/advanced/devicemap.html)

> Open mame.ini and locate the line with ctrlr and replace with

    ctrlr                     remapController

> In the ctrlr folder create a file and name it remapController.cfg
> Add this to the file.

    <mameconfig version="10">
    	<system name="default">
    		<input>
	    		<mapdevice device="XInput Player 1" controller="JOYCODE_1" />
    			<mapdevice device="XInput Player 2" controller="JOYCODE_2" />
    			<mapdevice device="vJoy Device product_bead1234-0000-0000-0000-504944564944 instance_a017c0e0-8c35-11ed-8005-444553540000" controller="JOYCODE_3" />
    			<mapdevice device="vJoy Device product_bead1234-0000-0000-0000-504944564944 instance_e251fde0-8c2b-11ed-8002-444553540000" controller="JOYCODE_4" />    			
    		</input>
    	</system>
    </mameconfig>
   

>  All you have to do now is find your own device so you can replace
> them in that file.
Run mame.exe -v in your mame folder in command line.
You should see line that will indicate your vJoy and XInput like this.
 

    Input: Adding joystick #1: vJoy Device (device id: vJoy Device product_bead1234-0000-0000-0000-504944564944 instance_a017c0e0-8c35-11ed-8005-444553540000)
    Input: Adding joystick #2: vJoy Device (device id: vJoy Device product_bead1234-0000-0000-0000-504944564944 instance_e251fde0-8c2b-11ed-8002-444553540000)
    Input: Adding joystick #3: XInput Player 1 (device id: XInput Player 1)
    Input: Adding joystick #4: XInput Player 2 (device id: XInput Player 2)

> You just copy your device id from the command line to the
> remapController.cfg file. The example should help you see what to copy
> where.

We need to do some more configuration in MAME to make it work.

> Launch MAME and adjust the deadzone to 0 General Settings -> Advanced
> Options -> Joystick deadzone -> 0
> Make sure you Save Settings on your way back.

For the configuration of MAME It can be tricky because every game is different but you should configure the movement in lightgun X Axis and Y Axis. You just have to move the light gun right for X axis and Up for Y Axis. Again this is tricky cause you have to only move a little and in a perfect line so it register correctly. Some other games use AD instead of lightgun but for all the weird stuff like that I usually look at the game specific input and assign them per game basis.

# Startup execution
When the arcade boots up here is the order program should launch

1. XOutput
2. Lichtknarre
3. Big Box (in my case)

# Desired experience
If everything is setup corretly you should be able to launch any MAME game and still have controls working for standard games. 
When you want to play a lightgun game you just have to press 1+2 on the wiimote to connect them to the computer and after launch the game.
When your done press power on the Wiimote to disconnect them.
But you can switch to a non-lightgun game without closing the wiimote.
No more controller hell and mixed up when connecting and disconnecting controller.

# What's next
A lot could be improved here but I hope it helps you getting your setup like you want it. If you have any suggestion or recommendation or correction please let me know.
