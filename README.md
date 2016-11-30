ofxGStreamer
============

ofxGStreamer is an special addon that allows to use the openFrameworks GStreamer elements, ofGstVideoPlayer and ofGstUtils under windows and osx

This addon has no code and instead uses the addon_config.mk file included here to add the required files from the core + the flags needed to compile programs using [GStreamer](http://gstreamer.freedesktop.org/). Please see [ofxGSTRTP](https://github.com/arturoc/ofxGSTRTP) for examples and implimentation of the toolkit. 

The instructions below will only work with the latest version of the [project generator](https://github.com/ofZach/projectGeneratorSimple) on github. More information about getting OF setup is documented [here](https://github.com/openframeworks/openFrameworks/blob/master/docs/table_of_contents.md). Use the project generater to create template projects after you have installed the GStreamer libraries according to the instructions below.

##Installing gstreamer

This addon depends on the gstreamer SDK. We are using version 1.0 currently. To install it:

###For OSX (Tested with XCode 5, 10.6,7,8,9,10)

First you need to download gstreamer v1.0. To install it under osx install each of the packages described below from the [gstreamer install packages for osx](http://gstreamer.freedesktop.org/data/pkg/osx/)
    
* [gstreamer-1.0-1.2.1-universal.pkg](http://gstreamer.freedesktop.org/data/pkg/osx/1.2.1/gstreamer-1.0-1.2.1-universal.pkg) installs the necesary libraries for applications to run

* [gstreamer-1.0-devel-1.2.1-universal.pkg](http://gstreamer.freedesktop.org/data/pkg/osx/1.2.1/gstreamer-1.0-devel-1.2.1-universal.pkg) installs the development files needed to compile gst applications

* [gstreamer-1.0-1.2.1-universal-packages.dmg](http://gstreamer.freedesktop.org/data/pkg/osx/1.2.1/gstreamer-1.0-1.2.1-universal-packages.dmg) contains some additional packages that are needed to encode some formats like h264, some of these packages have GPL license or use patented formats. Download the dmg and install the following packages from that collection:

This package is needed to do colorspace conversion:

    * gstreamer-1.0-libav-1.0.21-universal.pkg

And these packages will provide h264 encoding and some network utilities to stream h264 and other data formats:
    
    * gstreamer-1.0-codecs-restricted-1.0.21-universal.pkg
    
    * gstreamer-1.0-net-restricted-1.0.21-universal.pkg

If you only want to run an osx app, the development package is not needed but for our applications we needed to the additional packages

Note: on the systems we have tested it seems it's necesary to remove or rename `/Library/Frameworks/GStreamer.framework/Headers/assert.h` or it'll clash with the assert.h in the system and some projects won't compile. In our testing we renamed the file to keep a copy instead of deleting it. Open a terminal window and paste this in: 

````
sudo mv /Library/Frameworks/GStreamer.framework/Headers/assert.h /Library/Frameworks/GStreamer.framework/Headers/assertCOPY.h
````

###For Windows VS2015 / Windows 10
Depending on the configuration you want to compile your application with (32/64bit), download the corresponding installers.
It is **important** to install the complete version when the installer asks, or it won't install some plugins like h264 encoder and decoder, audio input... in general everything in gstreamer bad and ugly won't be installed with the tipical installation mode.

* 32bit
	* download and install [gstreamer-1.0-x86-1.7.1](http://gstreamer.freedesktop.org/data/pkg/windows/1.7.1/gstreamer-1.0-x86-1.7.1.msi)
	* download and install [gstreamer-1.0-devel-x86-1.7.1](http://gstreamer.freedesktop.org/data/pkg/windows/1.7.1/gstreamer-1.0-devel-x86-1.7.1.msi)
	* add "C:\gstreamer\1.0\x86\bin" to PATH environment variable
	* add Additional Library Direcory "D:\gstreamer\1.0\x86\lib" to the project in VS

* 64bit
	* [gstreamer-1.0-x86_64-1.7.1](http://gstreamer.freedesktop.org/data/pkg/windows/1.7.1/gstreamer-1.0-x86_64-1.7.1.msi)
	* [gstreamer-1.0-devel-x86_64-1.7.1](http://gstreamer.freedesktop.org/data/pkg/windows/1.7.1/gstreamer-1.0-devel-x86_64-1.7.1.msi)
	* add "C:\gstreamer\1.0\x86_64\bin" to PATH environment variable
	* add Additional Library Direcory "D:\gstreamer\1.0\x86_64\lib" to the project in VS
	
For both configurations, add these libraries to Linker->Additional Dependencies:
gstbase-1.0.lib
gstapp-1.0.lib
gstreamer-1.0.lib
gobject-2.0.lib
iconv.lib
glib-2.0.lib
gstvideo-1.0.lib

After installing those 2 packages, you need to add the path to the bin directory of the installation to the PATH environment variable. In windows 8 open the control panel and search for environment variable. In windows 7 type "environment variable" into the run window. Edit the system PATH variable and at the end by adding a ; and the bin folder in the path where you installed gstreamer. The default path should be:

`;C:\gstreamer\1.0\x86\bin`

That will allow you to run the gstreamer tools like gst-launch from the command line in any folder and will let the applications know where to find the runtime dll's.


### Instructions edit: 1/12/16

Additional bits I had to do:
C++ > Additional Include Directories
- C:\gstreamer\1.0\x86_64\lib\gstreamer-1.0\include

Linker > Additional Library Directories
- C:\gstreamer\1.0\x86_64\lib

Linker > Input > Additional Dependencies
- ;gstbase-1.0.lib;gstapp-1.0.lib;gstreamer-1.0.lib;gobject-2.0.lib;iconv.lib;glib-2.0.lib;gstvideo-1.0.lib

Environment variable (control panel > search for environment variable)
- edit the system environment variables
- change the value of GSTREAMER_1_0_ROOT_X86 to the same as GSTREAMER_1_0_ROOT_X86_64. eg. 'C:\gstreamer\1.0\x86_64\'
- alternatively if dont want to change the env var as above, you have to edit ofGstUtils.cpp line 124: string gst_path = g_getenv("GSTREAMER_1_0_ROOT_X86_64");

Use PG to create project with ofxGstreamer. To use Apple ProRes 4444 alpha encoded videos:
- player.setPixelFormat(OF_PIXELS_RGBA); // before loadMovie 

