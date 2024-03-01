![image](https://github.com/NDZL/-blog-WSC-DEV-UG/assets/11386676/de35f99b-9336-4df0-8ffa-fee642e2c61b)# -blog-WSC-DEV-UG
WSC User Guide for Developers blog: notes on the attached sample code.


Title: "Streamlining Workstation Connect Configuration: A Step-by-Step Guide for Developers"

## Foreword
This blog is an annex to the Zebra Workstation Connect User Guide for Developers (wsc-dev-ug.pdf) and is published at the following address https://developer.zebra.com/blog/streamlining-workstation-connect-configuration-step-step-guide-developers
 
It's aiming at supporting developers with a few annotated sample projects
- https://github.com/ZebraDevs/WSC-DEV-UG-SampleApp (Java)
- https://github.com/ZebraDevs/WSC-MAUI-SampleApp (.NET MAUI, C#)
- https://github.com/ZebraDevs/WSC-FLUTTER-SampleApp (Flutter, Dart/Java)

A more thorough introduction to Zebra Workstation Connect (acronyms: ZWC or WSC) is found in the Zebra DevCon 2023 archive. For slides and videos, visit

- **SLIDES DECK** https://www.zebra.com/content/dam/zebra_dam/en/presentation/customer-facing/zebra-devcon2023-presentation-customer-facing-workstation-connect-nicola-de-zolt-en-us.pdf
- **RECORDING** https://www.zebra.com/content/dam/zebra_dam/en/video/web-production/zebra-devcon2023-video-website-emc-power-pos-and-workstations-with-workstation-connect-nicola-de-zolt-en-us.mp4

## WSC pre-requisites

At the time of writing, WSC 2.0 is publicly available. Such version is only supported on Android 11, on Zebra devices that have installed a minimum LifeGuard Update version (see next table)

![image](https://github.com/NDZL/-blog-WSC-DEV-UG/assets/11386676/b133ebfe-b6fe-42b2-8503-6caa66e54d1e)

Starred (*) models additionally require the Mobility DNA Enterprise License.

Get the WSC launcher from https://www.zebra.com/us/en/support-downloads/software/productivity-apps/workstation-connect.html 


## Programmatically configuring WSC

WSC gets configured by means of a JSON configuration file, which holds all the restrictions to be applied.

This is an example of a JSON Configuration file

![image](https://github.com/NDZL/-blog-WSC-DEV-UG/assets/11386676/c12db127-00d6-480a-a943-5e962868d4d3)

In the example above, the feature with value=1 gets enabled, those with value=2 get disabled.

Developers have two options to transfer the configuration to WSC: via a Delegation Scope and via Secure Storage Manager (SSM).

   ### Using Delegation Scopes to configure WSC

- The delegation scope to use is ```delegation-zebra-workstationconnect-api-access-configuration-service```
- Your Administrator needs to grant your application the permission to call the service associated with that delegation scope. In order to do so, the following MX feature must be used, e.g. via Stagenow

    ![image](https://github.com/NDZL/-blog-WSC-DEV-UG/assets/11386676/b8ec45c2-c4a8-427f-b837-05d83178c3c8)

- The Service Identifier is the delegation scope name as shared above
- The Caller Package Name is your ```applicatioID``` as it appears the in module's gradle.build file
- The Caller Signature is your app public signature. You can extract it e.g. with https://techdocs.zebra.com/emdk-for-android/latest/samples/sigtools/


On the coding side, 
- add the following AIDL to your project, keeping name and package name unchanged

![image](https://github.com/NDZL/-blog-WSC-DEV-UG/assets/11386676/1210232c-48f6-44c7-8d9b-33dbda7e50a1)

![image](https://github.com/NDZL/-blog-WSC-DEV-UG/assets/11386676/6d5bbec9-6c4a-4f2e-8bc4-7b6be025e5af)




  





  






