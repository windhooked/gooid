#### Basic go compilation environment
First install the basic [go compilation environment](https://github.com/golang/go), which requires go1.10 or above. There are too many tutorials on the Internet for Golang installation, which are not listed here. Note that you need to install git and test it on the command line:
```dos
go version
git
```
Verify that the go environment is running normally.

#### Android ndk compiler configuration
Here is a table first:

GOARCH | TOOLPATH | LIBPATH | GCC_H | ABI |
---------|----------|---------|---------|---------
  arm | arm-linux-androideabi-4.9 | arm | arm-linux-androideabi | armeabi OR armeabi-v7a
  arm64 | aarch64-linux-android-4.9 | arm64 | aarch64-linux-android | arm64-v8a
  386 | x86-4.9 | x86 | i686-linux-android | x86
  amd64 | x86_64-4.9 | x86_64 | x86_64-linux-android | x86_64

Connect to the mobile phone or simulation, run `adb shell getprop ro.product.cpu.abilist` (or `adb shell getprop ro.product.cpu.abi`) on the command line to view the output results. For example, the simulation execution results of Andorid 7.0 arm are as follows:
```dos
>adb shell getprop ro.product.cpu.abilist
armeabi-v7a,armeabi
```

Now that we see that the ABI supports armeabi-v7a and armeabi, we can select one of them as the ABI we want to generate the application, usually armeabi or x86 (because 64-bit mobile phones are generally compatible with 32-bit). After determining the ABI, you can get the corresponding values such as TOOLPATH, GOARCH, etc. through the above table. According to Windows notation, the corresponding value is represented by %TOOLPATH%. For example, when the ABI is armeabi, %TOOLPATH% is arm-linux-androideabi- 4.9.

    1. [Download android-ndk-r16b-windows-x86_64.zip](https://developer.android.google.cn/ndk/downloads/)
    2. Unzip the downloaded android-ndk-r16b-windows-x86_64.zip file
    3. In the extracted android-ndk-r16b\toolchains directory, you can see several ????-4.9 directories, including android-ndk-r16b\toolchains\toolchains\%TOOLPATH%\, put android-ndk -r16b\sysroot Copy to the android-ndk-r16b\toolchains\toolchains\%TOOLPATH%\prebuilt\windows-x86_64\ directory
    4. Copy android-ndk-r16b\platforms\android-%SDK_API%\arch-%LIBPATH%\usr\lib to android-ndk-r16b\toolchains\toolchains\%TOOLPATH%\prebuilt\windows-x86_64\sysroot\ usr\ directory
        - If there is no such directory, you did something wrong in the previous step.
        - At this time, there should be two directories: include and lib under the android-ndk-r16b\toolchains\toolchains\%TOOLPATH%\prebuilt\windows-x86_64\sysroot\usr\ directory
    5. Copy android-ndk-r16b\toolchains\toolchains\%TOOLPATH%\prebuilt\windows-x86_64\sysroot\usr\include\%GCC_H%\asm to android-ndk-r16b\toolchains\toolchains\%TOOLPATH%\ prebuilt\windows-x86_64\sysroot\usr\include\ directory

**Add the full path of android-ndk-r16b\toolchains\toolchains\%TOOLPATH%\prebuilt\windows-x86_64\bin to the environment variable PATH**
You can run `%GCC_H%-gcc -v` on the command line, indicating that the NDK is ready.
 

#### Command line environment setup and compilation
1. Download [tools\](https://github.com/gooid/tools), get the corresponding windows or linux version of gomobile (the executable permissions must be set manually under linux), and set its path to the environment variable as well. in PATH
2. Open the command line and execute:
```dos
set GOPATH=???
setGOOS=android
set CGO_ENABLED=1
setGOARCH=%GOARCH%
set CGO_CFLAGS=-D__ANDROID_API__=%SDK_API%
set CC=%GCC_H%-gcc
set CGO_CXXFLAGS=-D__ANDROID_API__=%SDK_API%
set CXX=%GCC_H%-g++
```
3. Get [gooid](https://github.com/gooid/gooid)
```dos
go get github.com/gooid/gooid
```
4. Test the compilation, execute the following commands in sequence, and finally generate out\wallpapertwinkle.apk
```dos
cd %GOPATH%\src\github.com\gooid\gooid\examples\WallPaperTwinkle
go build -buildmode c-shared -o basic\lib\%ABI%\libWallPaperTwinkle.so
gomobile build -o out\wallpapertwinkle.apk basic
```

At this point, the environment is finally set up.
