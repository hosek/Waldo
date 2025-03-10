# Waldo

## Get the apk

### Extract from device
```
adb shell "pm list packages -f" | grep idea
package:/data/app/~~iZYB7PUXWPonjfSJuF3xVg==/com.ideashower.readitlater.pro-SARvpi0O4DwJHjH3pwoCvA==/base.apk=com.ideashower.readitlater.pro

adb pull /data/app/~~iZYB7PUXWPonjfSJuF3xVg==/com.ideashower.readitlater.pro-SARvpi0O4DwJHjH3pwoCvA==/base.apk

```

### Use 3rd party service like apkpure


## Decompile akp
```
 apktool d base.apk
```

### Search for proper location

I used VSC and found smali_classes2/com/pocket/app/MainActivity.smali 
What later on turns out to be bad choice - I havent run the applikaction after installing, so I overlooked that there is registration screen and expected Main Activity as ideal place where to start, I would go with Application class now.

*This cause that you have to skip registration first to see the logcat :/*

Then I could use jadex to try to see the code and match it with smali files, but haven't it on my machine and installion takes ages so for this easy change I went with direct smali edit

http://pallergabor.uw.hu/androidblog/dalvik_opcodes.html

smali_classes2/com/pocket/app/MainActivity.smali:

```
.method public onCreate(Landroid/os/Bundle;)V
    .locals 6

    .line 1
    invoke-super {p0, p1}, Lcom/pocket/app/s0;->onCreate(Landroid/os/Bundle;)V

    .line 2
    const-string v0, "ROMAN"
    const-string v1, "Hello Waldo"
    invoke-static {v0, v1}, Landroid/util/Log;->v(Ljava/lang/String;Ljava/lang/String;)I
```

I found smali code for logcat here:
https://gist.github.com/xxnjdlys/e2c03544dde5f09c2950

Alternatively I could make dummy app with code I needed, decompile and extract the code, this was just faster.

## Build the app

```
apktool d base.apk -o base.apk
```

Then I hit this issue so I had to go back and edit Manifest file
https://github.com/iBotPeaches/Apktool/issues/1626

```
extractNativeLibs=true
```

## Sign the app

And finally make dummy keystore and sign the app

```
 ./apksigner sign --ks keystore.jks base.apk
```

## TEST the app

``` 
adb install final.apk
adb logcat
```

Run the app and skip the registration screen (sorry about it)
<img width="943" alt="Screenshot 2025-03-10 at 11 04 26" src="https://github.com/user-attachments/assets/0811999c-ac74-4765-a64d-036a59bf8b1c" />
