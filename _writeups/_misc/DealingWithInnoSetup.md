# Dealing with InnoSetup packed malwares/binaries

This method of packaging a malware bugged me for a while at first until I discovered that there was a way of simply unpacking it rather than tracing the whole process... 

![Dumb](/_storage/_img/DealingWithInnoSetup/Dumb.gif)

## What is InnoSetup 

InnoSetup is basically another utility to pack necessary components of the software into one single installer (like MSI installers lets say but this one's different), which attackers are using to evade certain security measures or for adding additional funcionality to a malware.

#### First Things First... Assuring that the malware is packaged with InnoSetup

Passing the binary to ExeinfoPE can assure that the malware is packaged with InnoSetup  
  
![DettectingInnoSetup](/_storage/_img/DealingWithInnoSetup/DetectingInnoSetup.png)


#### Unpacking with innounp

You can find [innounp](/_storage/_binaries/innounp.exe) in _storage/_binaries/innounp.exe 

Run `innounp.exe -x <binary_name>`
![RunningInnounp](/_storage/_img/DealingWithInnoSetup/RunningInnounp.png)

And all of the configuration files and binaries are extracted from the setup...

![UnpackedResult](/_storage/_img/DealingWithInnoSetup/UnpackedResult.png)


**Happy Hunting**  
  
![HappyHunting](/_storage/_img/DealingWithInnoSetup/HappyHunting.gif)

