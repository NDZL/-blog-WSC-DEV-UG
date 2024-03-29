<br>  

## Foreword
This blog is an annex to the Zebra Workstation Connect User Guide for Developers (wsc-dev-ug.pdf) and is published at the following address [https://developer.zebra.com/blog/streamlining-workstation-connect-configuration-step-step-guide-developers](https://developer.zebra.com/blog/streamlining-workstation-connect-configuration-step-step-guide-developers)
 
It aims to support developers with a few annotated sample projects
- [https://github.com/ZebraDevs/WSC-DEV-UG-SampleApp](https://github.com/ZebraDevs/WSC-DEV-UG-SampleApp) (Java)
- [https://github.com/ZebraDevs/WSC-MAUI-SampleApp](https://github.com/ZebraDevs/WSC-MAUI-SampleApp) (.NET MAUI, C#)
- [https://github.com/ZebraDevs/WSC-FLUTTER-SampleApp](https://github.com/ZebraDevs/WSC-FLUTTER-SampleApp) (Flutter, Dart/Java)

A more thorough introduction to Zebra Workstation Connect (acronyms: ZWC or WSC) is found in the Zebra DevCon 2023 archive. For slides and videos, visit

- [**SLIDES DECK**](https://www.zebra.com/content/dam/zebra_dam/en/presentation/customer-facing/zebra-devcon2023-presentation-customer-facing-workstation-connect-nicola-de-zolt-en-us.pdf)
- [**RECORDING**](https://www.zebra.com/content/dam/zebra_dam/en/video/web-production/zebra-devcon2023-video-website-emc-power-pos-and-workstations-with-workstation-connect-nicola-de-zolt-en-us.mp4)
 
<br>

## Workstation Connect (WSC) pre-requisites

At the time of writing, WSC 2.0 is publicly available. Such version is only supported on Android 11, on Zebra devices that have installed a minimum LifeGuard Update version (see next table)

![image](https://github.com/NDZL/-blog-WSC-DEV-UG/assets/11386676/b133ebfe-b6fe-42b2-8503-6caa66e54d1e)

Starred (*) models additionally require the Mobility DNA Enterprise License.

Get the WSC launcher from [https://www.zebra.com/us/en/support-downloads/software/productivity-apps/workstation-connect.html ](https://www.zebra.com/us/en/support-downloads/software/productivity-apps/workstation-connect.html)

<br> 

## Programmatically configuring WSC

WSC gets configured using a JSON configuration file, which holds all the restrictions to be applied.

This is an example of a JSON Configuration file

![image](https://github.com/NDZL/-blog-WSC-DEV-UG/assets/11386676/c12db127-00d6-480a-a943-5e962868d4d3)

In the example above, the feature with value=1 gets enabled, and those with value=2 get disabled.

Here you can compare the original launcher (left) and how the launcher appears after the configuration is applied (right)

![image](https://github.com/NDZL/-blog-WSC-DEV-UG/assets/11386676/2dd379b0-ec3c-4665-aa25-766517bb75eb)

The *All apps* button is disabled, and the apps running on the mobile display are not listed


Developers have two options to transfer the configuration to WSC: via a Delegation Scope and Secure Storage Manager (SSM).

<br> 

   ### Using Delegation Scopes to configure WSC

- The delegation scope to use is ```delegation-zebra-workstation connect-api-access-configuration-service```
- Your Administrator needs to permit your application to call the service associated with that delegation scope. To do so, the following MX feature must be used, e.g. via Stagenow

    ![image](https://github.com/NDZL/-blog-WSC-DEV-UG/assets/11386676/b8ec45c2-c4a8-427f-b837-05d83178c3c8)

- The Service Identifier is the delegation scope name as shared above
- The Caller Package Name is your ```applicationID``` as it appears the in module's gradle.build file
- The Caller Signature is your app's public signature. You can extract it e.g. with [https://techdocs.zebra.com/emdk-for-android/latest/samples/sigtools/](https://techdocs.zebra.com/emdk-for-android/latest/samples/sigtools/)


On the coding side, 
- add the following AIDL to your project, keeping the name and package name unchanged [(GitHub reference)](https://github.com/ZebraDevs/WSC-DEV-UG-SampleApp/blob/b307fcecfdc33f7beac68ff04cc5ee2a12bd1f97/app/src/main/aidl/com/zebra/valueadd/IZVAService.aidl#L8)

    ![image](https://github.com/NDZL/-blog-WSC-DEV-UG/assets/11386676/1210232c-48f6-44c7-8d9b-33dbda7e50a1)

    ![image](https://github.com/NDZL/-blog-WSC-DEV-UG/assets/11386676/6d5bbec9-6c4a-4f2e-8bc4-7b6be025e5af)

- then implement a ```ServiceConnection``` interface and in the ```onServiceConnected``` get an instance of the service binder ```iServiceBinder = com.zebra.valueadd.IZVAService.Stub.asInterface(service);```
- in the ```onStart``` method you can finally bind the WSC service by calling [(Github reference)](https://github.com/ZebraDevs/WSC-DEV-UG-SampleApp/blob/b307fcecfdc33f7beac68ff04cc5ee2a12bd1f97/app/src/main/java/com/zebra/wsc_exerciser/HDLauncherActivity.java#L156)

    ```
      private void bindtoZVAService() {
        Intent intent = new Intent("com.zebra.workstationconnect.release");
        intent.setClassName("com.zebra.workstationconnect.release", "com.zebra.workstationconnect.DeviceManagementService");
        bindService(intent, this, BIND_AUTO_CREATE);
    }
  ```
- eventually, the configuration gets transferred to the equipment by calling one single API [(See here)](https://github.com/ZebraDevs/WSC-DEV-UG-SampleApp/blob/b307fcecfdc33f7beac68ff04cc5ee2a12bd1f97/app/src/main/java/com/zebra/wsc_exerciser/HDLauncherActivity.java#L113)
    `iServiceBinder.processZVARequest( JSON Configuration string ) `

<br> 

### Using Secure Storage Manager (SSM) to configure WSC

This method is described in the WSC Administrator's Guide, on page 46 [https://www.zebra.com/content/dam/zebra_new_ia/en-us/manuals/software/workstation-connect/wsc-ag-en.pdf](https://www.zebra.com/content/dam/zebra_new_ia/en-us/manuals/software/workstation-connect/wsc-ag-en.pdf)

[Refer to this code sction](https://github.com/ZebraDevs/WSC-DEV-UG-SampleApp/blob/b307fcecfdc33f7beac68ff04cc5ee2a12bd1f97/app/src/main/java/com/zebra/wsc_exerciser/HDLauncherActivity.java#L188)
- Create the desired configuration as a JSON string
- Persist it as a file in a folder that can be accessed by any apps
- Feed the file to SSM using this target URI
    `com.zebra.workstationconnect.release/enterprise/workstation_connect_config.txt`

To learn how SSM works with files, refer to [https://techdocs.zebra.com/ssm/1-1/guide/share-files/](https://techdocs.zebra.com/ssm/1-1/guide/share-files/)

<br> 

## Working with Desktop Shortcuts

To dynamically manage the apps' shortcuts that appear on the secondary launcher, do the following
- manually create a set of shortcuts and export them by right-clicking the mouse on the launcher free space. Shortcuts get exported in the `/enterprise/usr/Export_Shortcuts_DATE-TIME.txt` file. The shortcuts are persisted as JSON values.

    ![image](https://github.com/NDZL/-blog-WSC-DEV-UG/assets/11386676/614b7f7a-abe8-4cab-b27d-d9c96ff9e385)

- in the Config.json file use the `shortcutImportFile` API, which is part of the `configDesktopShortcuts` key
- as a value, pass the exported shortcuts
    - be mindful to escape with a backslash `\` the imported JSON shortcuts

    ![image](https://github.com/NDZL/-blog-WSC-DEV-UG/assets/11386676/7184682a-f59c-41f1-a46c-74af4bab3574)

    - you can drop the `checksum` key if you edit the shortcuts since this field is optional

      ![image](https://github.com/NDZL/-blog-WSC-DEV-UG/assets/11386676/06c56193-d071-4dda-9c6f-12adc55416ad)

<br> 

 ## Techdocs Documentation and Roadmap
 
- Workstation Connect is also documented here [https://techdocs.zebra.com/zwc/2-0/about/](https://techdocs.zebra.com/zwc/2-0/about/)

- A Developers’ User Guide will be published along with this blog

- For Enterprise Browser APIs, refer to 
[https://techdocs.zebra.com/enterprise-browser/latest/guide/zwc/#apisforzwc ](https://techdocs.zebra.com/enterprise-browser/latest/guide/zwc/#apisforzwc )

- WSC v3 will be made available in March 2024 and support for Android 13 is added!
