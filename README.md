# SAR-opal-base 

Opal is a generalized Unity game builder designed for use in child-robot
interactions. Easily load different graphics for games requiring similar
mechanics, all using ROS.

## Build and Run
This game was built and tested with:

- Unity 5.3.5
- MonoDevelop 5.9.6
- rosbridge from ROS Indigo
- sar\_opal\_msgs 4.0.0
- TouchScript 8.1
- LeanTween [no current version number, latest commit was
  78b0458171150ed89aba7435f336099f7a81e26b from June 11, 2016, after 2.32
  release]
- websocket-sharp [no version number, latest commit was
  0ef00bf0a7d526fa705e938f1114d115691a377a from June 11, 2016]

## Configuration

The game uses configuration options listed in the opal\_config file. There
is an example file located in Assets/Resources/.

### On OS X

When running the game on OS X from the Unity editor, the game will check for
"Assets/Resources/opal\_config.txt".

### On Android

When running the game on Android (e.g., on a tablet), the game will first check
for "mnt/sdcard/edu.mit.media.prg.sar.opal.base/opal\_config.txt". This is
because once the game is packaged up for Android, you can't change stuff in the
package, so we needed a location for the config file that could be easily
edited after the game is installed. If that file doesn't exist, or connecting
fails with the values listed in that file, the game will try default values set
in the packaged game.

### On Linux

When running the game on Linux from a standalone executable, the game will
check for the config file in the "executable-name\_Data/Resources" directory.
You will have to manually add the config file to this directory. This is
because when the app is packaged up, all the game assets are packaged up by
Unity and cannot be easily edited after the game is installed. If that file
doesn't exist, or if connecting fails with the values listed in that file, the
game will try default values set in the packaged game.

If you are running the game in an Ubuntu virtual machine (e.g., using
VirtualBox), you may encounter OpenGL problems, possibly due to how the VM
handles 3D acceleration. One possible solution is to run the executable from
the command line with the flag `-force-opengl`, i.e., `./executable-name
-force-opengl`.

### Configuration options 

- server: [string] the IP address or hostname of the ROS server
- port: [string] port number to use
- toucan: [boolean] whether or not you want a toucan sidekick in the game
- log\_debug\_to\_ros: [boolean] whether or not to log Unity's Debug.Log\*
  calls to the ROS topic "/opal\_tablet".

#### Server & port On startup, the game will try to connect to the specified IP

address or host name with the specified port. The server listed should be the
IP address or hostname of the machine running roscore and the
rosbridge\_server.

You can start the rosbridge\_server with the command `roslaunch
rosbridge\_server rosbridge\_websocket.launch`.

If the specified server address does not exist on the network, there is a 90s
timeout before it'll give up trying (hardcoded in the websocket library, so one
could patch the library to change the timeout length if one so desires). This
will manifest as the application hanging, unresponsive, for the duration of the
timeout.

If the server address does exist but if you've forgotten to start
rosbridge\_server, the connection will be refused.

#### Toucan

If you set the toucan option to 'true', a toucan sidekick character will be
loaded into the game (provided you have the toucan graphics in your Assets
folder). You will then be able to send the sidekick commands, such as to play
back sound or animations. If set to false, no toucan will be present.

## SAR Opal messages

The game subscribes to the ROS topic "opal\_tablet\_command" to receive
messages of type
"/[sar\_opal\_msgs](https://github.com/personal-robots/sar_opal_msgs
"/sar\_opal\_msgs")/OpalCommand".

The game publishes /std\_msgs/String messages to the ROS topic "opal\_tablet".

The game publishes
"/[sar\_opal\_msgs](https://github.com/personal-robots/sar_opal_msgs
"/sar\_opal\_msgs")/OpalAction" to the ROS topic "opal\_tablet\_action". See
[/sar\_opal\_msgs] (https://github.com/personal-robots/sar_opal_msgs
"/sar\_opal\_msgs") for more info.

The game publishes
"/[sar\_opal\_msgs](https://github.com/personal-robots/sar_opal_msgs
"/sar\_opal\_msgs")/OpalScene" to the ROS topic "opal\_tablet\_scene". Usually
this message will only be published after receiving a "request keyframe"
command - see
[/sar\_opal\_msgs](https://github.com/personal-robots/sar_opal_msgs
"/sar\_opal\_msgs") for more info. 

The game publishes "/std\_msgs/Bool" to the ROS topic "opal\_tablet\_audio", to
indicate whether the sidekick character is done playing back an audio file.

### OpalCommand LOAD\_OBJECT messages

When Opal receives a LOAD\_OBJECT OpalCommand message, it will attempt to load
a GameObject with the specified properties. As the
[/sar\_opal\_msgs](https://github.com/personal-robots/sar_opal_msgs
"/sar\_opal\_msgs") documentation states, one of the fields you provide when
telling Opal to load an object is the name of the associated graphic to load.
Opal tries to load the graphic from two places:

1) "Assets/Resources/graphics/base-images" directory. If the game is a social
stories game (see below for different game descriptions), Opal assumes all
graphics loaded will be in the
"Assets/Resources/graphics/base-images/socialstories" directory. Either the
base-images or socialstories directories may have subdirectories, which
shouldsocialstories be provided as part of the graphic name. E.g.,if you want
to load the image "happy.png" that resides in the "base-images/emotions/"
directory, you would need to list the name of the graphic to load as
"emotions/happy.png".

2) If loading from Resources fails, Opal assumes the graphic file name provided
is actually a full file path to the desired image (that may or may not be in
the Resource directory), and attempts to load the graphic from that full file
path. If this fails, the graphic is not loaded and creation of the GameObject
will fail.

## Log Files

Unity automatically logs all Debug.Log\* calls to a text file. The location of
this file varies by platform -- [here's the official
list](http://docs.unity3d.com/Manual/LogFiles.html).

Opal has a configuration option that lets you decide whether you want these log
messages to be published to a ROS topic. If you do, note that initial log
messages will not show up on the ROS topic, since only messages that occur
*after* the websocket connection is setup can be logged.


## Submodules

You don't need to pull in these submodules for the main project to run (the
necessary scripts or dlls have been copied into the Assets/Plugins folder), but
if you want their source, extra examples, prefabs, etc, then you can.

### TouchScript

[TouchScript] (https://github.com/TouchScript/TouchScript "TouchScript") makes
it easy to detect and respond to touch events, such as taps and drags. See the
wiki [here] (https://github.com/TouchScript/TouchScript/wiki "TouchScript
wiki") for more information. 

We built a unitypackage from the TouchScript 8.1 source, which has been
imported into the game in the Assets folder.

Instructions on building TouchScript's unitypackage from source are online
[here] (https://github.com/TouchScript/TouchScript/wiki/How-to-Contribute "How
to Contribute"). For 8.1, the steps are:

- init and update TouchScript's git submodules
- init and update any submodules of those submodules
- run `Build/build_external.sh`
- run `Build/package.sh`
- import the generated TouchScript.unitypackage file in the Unity editor 

Note that the MainCamera and the Moveables Camera in the Unity scene each need
a CameraLayer2D component attached. The camera layer is used to "see" which
objects in the scene can be touched - see [Layers]
(https://github.com/TouchScript/TouchScript/wiki/Layers "TouchScript Layers").
If you don't have a camera layer of some kind attached to the MainCamera,
TouchScript will automatically add one, but the default is a CameraLayer that
handles 3D objects and 3D colliders. Since Opal is a 2D game, we need to use
the CameraLayer2D, which is for 2D objects and 2D colliders.  (Emphasizing this
extra because it can cause needless headache.)

The TouchScript game object should have a Touch Manager component attached,
which will list the different camera layers in the scene.

### LeanTween

[LeanTween](https://github.com/dentedpixel/LeanTween/ "LeanTween git") is a
library for animating sprites ([docs
here](http://dentedpixel.com/LeanTweenDocumentation/classes/LeanTween.html
"LeanTween docs")).

If you pull in the submodule, you can get the examples, prefabs, etc. The
necessary .cs files are in the SAR-opal-base Assets/Plugins directory already.
Note that the LeanTween instructions only tell you to move LeanTween.cs to your
Plugins directory; however, you actually need several other files that are in
LeanTween's Plugins directory as well.

### websocket-sharp

[Websocket-sharp](https://github.com/sta/websocket-sharp "websocket-sharp git")
is a .Net implementation of websockets, and is used to communicate with the ROS
rosbridge\_server.

Note that if you try to build this project, the Newtonsoft.Json dll appears to
be missing. However, a dll is built and placed in bin/Debug anyway. Some
functionality may be missing as a result, but it doesn't seem to be necessary
for this project.

### MiniJSON

[MiniJSON](https://gist.github.com/darktable/1411710 "miniJSON") is a pretty
basic C# JSON encoder and decoder. It can serialize and deserialize JSON
strings, which are sent to and received from the ROS rosbridge\_server. I've
used the code mostly as-is, with one or two of the bug fixes listed in the
comments on the github gist page added in.


## Miscellaneous Notes

- When adding new images to the project, make sure to set each image as
  'Advanced' and check the 'read/write' box in the Unity editor. If you don't
  do this, when images are programatically loaded as PlayObjects, the polygon
  colliders won't be generated to properly fit the image's shape/outline.
  When deploying the game, you'll probably get the error "Sprite outline
  generation failed - could not read texture pixel data. Did you forget to
  make the texture readable?" whenever you dynamically add a polygon
  collider to an object. Something about textures/images not being readable
  by scripts by default, the polygon collider needing to read the texture
  to figure out the outline to make the collider the right shape, but not
  being able to, and thus the collider ending up the wrong shape and making
  collisions happen weird... 

- Only a small set of "demo" graphics are included in this repository. The full
  set of graphics for different games is available from the Personal Robots
  Group - email students in the group to inquire. Add the full set to the
  "Resources\/base\_images" folder. 

## Version Notes

The Year 3 SAR study was run using Opal version 1.0.3.

The Cyber4 study was run using Opal version 2.0.0.

## Game Versions

Opal is a flexible platform and can be configured for a number of different
game setups. Currently, these include:

- Demo
- SAR Year 3 study game with Toucan
- Storybook
- SAR Year 5 social stories game

### Demo Version

To build and deploy the demo version, do the following:

1. In Unity > Build Settings > Scenes in build, check all the demo scenes and
   uncheck basic-scene and all other non-demo scenes.
2. In Unity > Build Settings > Player Settings, change the deployed name of the
   game to be "SAR Opal Demo" and the bundle identifier to be "demo" instead of
   "base". Technically, this step is not necessary, but if you have another
   version of Opal deployed on your device, you'll want to change these
   settings so you can have both side by side.
3. In the MainGameController, set the flag "demo" to true, "story" to false,
   and "socialStories" to false.
4. Build and deploy. 

The demo version of the game requires some graphics that are not included in
the demo. 

### Storybook Version

To build and deploy the Frog Where Are You storybook, do the following:

1. Get the Frog Where Are You graphics and put them in the
   "Resources/graphics/base-images/frogwhereareyou/" directory.
1. In Unity > Build Settings > Scenes in build, check the frog-where-are-you
   scene and uncheck the other scenes.
2. In Unity > Build Settings > Player Settings, change the deployed name of the
   game to be "Frog Where Are You" and the bundle identifier to be "fway"
   instead of "base". Technically, this step is not necessary, but if you have
   another version of Opal deployed on your device, you'll want to change these
   settings so you can have both side by side.
3. In the MainGameController, set the flag "story" to true, "demo" to false,
   and "socialStories" to false.
4. Build and deploy.

You can use the Frog Where Are You book as an example for how to load your own
set of images for a storybook.

### Social Stories Version

To build and deploy the Social Stories version, do the following:

1. Get the Social Stories graphics and put the "socialstories" directory in the
   "Resources/graphics/base-images/" directory.
2. In Unity > Build Settings > Scenes in build, check the socialstories scene
   and uncheck the other scenes.
3. In Unity > Build Settings > Player Settings, change the deployed name of the
   game to be "SAR Social Stories" and the bundle identifier to be "ss" instead
   of "base". Technically, this step is not necessary, but if you have another
   version of Opal deployed on your device, you'll want to change these
   settings so you can have both side by side.
3. In the MainGameController, set the flag "story" to false, "demo" to false",
   and "socialStories" to true. 
4. Build and deploy.

## Bugs and Known Issues

Games made with Unity 5 cannot be deployed to non-neon devices (i.e., Android
tablets that have tegra boards, such as many of the older Samsung Galaxy
tablets), because Unity 5 no longer supports these devices. Thus, Opal cannot
be deployed to these devices.

### Reporting Bugs

Please report all bugs and issues on the [SAR-opal-base github issues
page](https://github.com/personal-robots/SAR-opal-base/issues).

## TODO

- 'next page' appears to not work when touch disabled
- Add filepath to SceneObjectProperties so objects can be loaded from outside
  build directory
- LoadSpriteFromFile does not fail when a non-image file, such as a text file,
  is loaded, but should fail
- Add guidelines for where to put images on tablet for loading images from
  outside build directory and test with tablet
- Load sounds from a folder outside build directory
- Update to load images and audio from folders on the tablet's sdcard
  (currently, must load from the Resources folder, which gets compiled in to
  the Unity game)
- Create demo version of the game that only uses graphics that are checked in
  to the repository.
- Move 'highlight' object with transformer, currently does not follow drag path
  very well
- Log all log messages locally to tablet
- Objects can leave the viewable screen on drag, changes margins (this is
  because we used TouchScript's Transformer for drag, which doesn't have the
  margins for where not to go)
- Add some way of easily seeing which objects in the scene are draggable or
  able to be interacted with; used to do the grow-shrink pulse motion, but that
  caused havoc with the collision detection
- Right now, the sidekick configuration is a simple true/false; you can't start
  out without a sidekick and add it in later. Consider adding a sar\_opal\_msg
  that enables or disables the sidekick, so that it can appear or disappear as
  needed.
- Add capability to load an image on the "top left" or "bottom right" of the
  screen without specifying exact coordinates. Adjust the loaded image's
  position until no collisions are detected so it does not overlap with other
  images.
- Adapt all games to different screen sizes (currently size of images in most
  games is hard-coded, not scaling - social stories game is the only one that
  scales properly)
- Look into ROS .NET for C#. Possible replacement for websocket connection?
- Add demo and story options to config file so you don't have to recompile and
  redeploy (and/or a start screen that lets you pick whether you want demo
  mode, story mode, etc when you start the game - use "load scene" function to
  pick the right one).
