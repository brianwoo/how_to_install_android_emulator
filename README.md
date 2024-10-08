# How to install Android Emulator without Android Studio


## Installing the Android SDK
- First, go to the Android Studio download page: https://developer.android.com/studio
- Then click in “Download Options”
- There you will find a table named “Command line tools only”;
- This table contain some zip files. Download the appropriate file for your system (Windows, Mac or Linux);

Create a folder anywhere you prefer to place your SDK. I recommend you to stick with one of these commonly used places:

```bash
## Windows:
# Globally: 
C:\Android\sdk or C:\android-sdk 

# One user only: 
C:\Users\<username>\AppData\Local\Android\sdk

## Linux
# Globally: 
/opt/android/sdk or /opt/android-sdk

# One user only: 
/home/<username>/.android/sdk
```

```bash
# Unzip the Unzip the commandlinetools-xxx.zip
# Put the extracted cmdline-tools directory under your sdk directory:
<sdk>/cmdline-tools
```

<br>

## Install Java 8

```bash
# Windows:
https://www.oracle.com/ca-en/java/technologies/javase/javase8-archive-downloads.html

# Linux:
sudo apt update
sudo apt install openjdk-8-jdk
```
## 
<br>

## Download other essential packages
```bash
cd <sdk>/cmdline-tools/tools/bin

./sdkmanager platform-tools emulator

This will download the platform-tools and emulator under <sdk>
```
<br>

## Set your environment variables
```bash
export ANDROID_SDK_ROOT="<your sdk directory>"
export PATH="${PATH}:${ANDROID_SDK_ROOT}/cmdline-tools/tools/bin:${ANDROID_SDK_ROOT}/emulator:${ANDROID_SDK_ROOT}/platform-tools"
```
<br>

## Download a system image and other packages
```bash
./sdkmanager --list

sdkmanager --list | grep system-images

# You will see a lot of system-images. Pick a version to download with playstore. e.g.:
sdkmanager "system-images;android-30;google_apis_playstore;x86_64"

sdkmanager "platforms;android-30"

sdkmanager "build-tools;android-30.0.3"
```
<br>

## Create an AVD device
```bash
avdmanager --verbose create avd \
 --name nexus5 \
 --package "system-images;android-30;google_apis_playstore;x86_64"
```
<br>

## Add keyboard and GPU support
```bash
cd .android/avd/nexus5.avd

vi config.ini

# Change the following to yes
hw.keyboard = yes
hw.mainKeys = yes
hw.gpu.enabled = yes
hw.gpu.mode = auto
```
<br>

## Run the emulator
```bash
emulator -avd nexus5 -partition-size 16384
```
<br>


## Rooting (Without Google Play) & Installing BurpSuite Certificate

[Guide](https://book.hacktricks.xyz/mobile-pentesting/android-app-pentesting/install-burp-certificate)

e.g. system-images;android-33;google_apis;x86_64
Start emulator with "Writable System"
```bash
emulator -avd "rooted" -writable-system
```

```bash
openssl x509 -inform DER -in burp_cacert.der -out burp_cacert.pem
CERTHASHNAME="`openssl x509 -inform PEM -subject_hash_old -in burp_cacert.pem | head -1`.0"
mv burp_cacert.pem $CERTHASHNAME #Correct name
adb root && sleep 2 && adb remount #Allow to write on /syste
adb push $CERTHASHNAME /sdcard/ #Upload certificate
adb shell mv /sdcard/$CERTHASHNAME /system/etc/security/cacerts/ #Move to correct location
adb shell chmod 644 /system/etc/security/cacerts/$CERTHASHNAME #Assign privileges
adb reboot #Now, reboot the machine
```

## Rooting (with Google Play) & Installing BurpSuite Certificate

- Requires RootAVD / Magisk
- [RootAVD GitLab](https://gitlab.com/newbit/rootAVD)
  - RootAVD tested with Android 33 with Playstore
  - ./rootAVD.sh /opt/android/sdk/system-images/android-33/google_apis_playstore/x86_64/ramdisk.img
- [Guide](https://book.hacktricks.xyz/mobile-pentesting/android-app-pentesting/install-burp-certificate)
- [Video](https://www.youtube.com/watch?v=qQicUW0svB8)



#### References:
https://medium.com/michael-wallace/how-to-install-android-sdk-and-setup-avd-emulator-without-android-studio-aeb55c014264

https://gist.github.com/mrk-han/66ac1a724456cadf1c93f4218c6060ae

https://stackoverflow.com/questions/11235370/android-emulator-doesnt-take-keyboard-input-sdk-tools-rev-20

