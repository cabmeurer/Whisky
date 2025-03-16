# Running Steam Games on Apple Silicon Macs with Whisky

This guide provides a complete walkthrough for getting Steam working in Whisky on Apple Silicon Macs. This approach has been successfully tested on M1 Pro Max running macOS Sonoma.

## Table of Contents
- [Introduction](#introduction)
- [Prerequisites](#prerequisites)
- [Building Whisky from Source](#building-whisky-from-source)
- [Installing Steam](#installing-steam)
- [Fixing Steam Launch Issues](#fixing-steam-launch-issues)
- [Optimizing for Gaming](#optimizing-for-gaming)
- [Troubleshooting](#troubleshooting)
- [Game-Specific Tips](#game-specific-tips)

## Introduction

Whisky is an open-source Wine wrapper for macOS that provides a clean graphical interface for running Windows software. This guide specifically focuses on getting Steam working properly, which can be challenging due to various compatibility issues between Steam and Wine.

## Prerequisites

- Apple Silicon Mac (M1, M2, M3 series)
- macOS Sonoma 14.0 or newer
- Xcode installed (for building from source)
- Homebrew (recommended)
- At least 30GB of free space (more if you plan to install games)

## Building Whisky from Source

1. **Clone the Whisky repository**
   ```bash
   git clone https://github.com/Whisky-App/Whisky.git
   cd Whisky
   ```

2. **Install SwiftLint if needed**
   ```bash
   brew install swiftlint
   ```

3. **Open and build the project**
   - Open `Whisky.xcodeproj` in Xcode
   - Select your Mac as the build target
   - Build and run the project (⌘R)

   Alternatively, build from the command line:
   ```bash
   xcodebuild -project Whisky.xcodeproj -scheme Whisky -configuration Release
   ```

4. **First launch setup**
   - Follow the on-screen instructions to set up Whisky
   - This will install Rosetta 2 if not already installed
   - Whisky will download and install Wine components

## Installing Steam

1. **Create a new bottle**
   - Launch Whisky
   - Click the "+" button to create a new bottle
   - Name it "Steam" or your preference
   - Select Windows 10 as the Windows version
   - Click "Create"

2. **Download the Steam installer**
   - Download the Windows version of Steam: [Steam Setup](https://store.steampowered.com/about/)
   - Save it to a convenient location

3. **Install Steam**
   - In Whisky, select your Steam bottle
   - Click "Run .exe..." or drag the Steam installer onto the Whisky window
   - Follow the Steam installation wizard
   - Let it install to the default location
   - Uncheck "Run Steam" at the end of installation

## Fixing Steam Launch Issues

Steam on Wine often encounters update and verification errors. Here's how to fix them:

1. **Locate your Steam executable**
   - In Whisky, right-click on the Steam icon and select "Show in Finder"
   - Note the path to your Steam installation

2. **Using Terminal to fix Steam**
   - In Whisky, click on the "Terminal..." button to open a macOS terminal
   - Navigate to the Steam folder:
     ```bash
     cd "<paste_your_steam_path_here>"
     ```
     For example:
     ```bash
     cd "/Users/username/Library/Containers/com.isaacmarovitz.Whisky/Bottles/<bottle_id>/drive_c/Program Files (x86)/Steam"
     ```

3. **Force download a compatible Steam version**
   - Run this command to force Steam to download a compatible version:
     ```bash
     wine steam.exe -forcesteamupdate -forcepackagedownload -overridepackageurl http://web.archive.org/web/202405201f_/media.steampowered.com/client -exitsream
     ```
   - This will download an older version of Steam that works better with Wine and then exit
   - note: keep a lookout for any failures and/or missing dependencies before moving on.

4. **Configure Steam launch arguments**
   - In Whisky, right-click on Steam → Config
   - Add these arguments to prevent update/verification issues:
     ```
     -noverifyfiles -nobootstrapupdate -skipinitialbootstrap -norepairfiles -overridepackageurl
     ```

5. **Launch Steam**
   - Click "Run..." to start Steam with the new configuration
   - You should now see the Steam login screen

## Optimizing for Gaming

Once Steam is running, follow these steps for better gaming performance:

1. **Install Winetricks components** (if needed)
   - In your Steam bottle, go to Config → Winetricks
   - Install these components for better compatibility:
     - corefonts
     - d3dx9
     - vcrun2019

2. **Steam settings**
   - In Steam Settings → Interface → Disable "GPU accelerated rendering in web views"
   - In Steam Settings → Shader Pre-Caching → Disable all options
   - In Steam Settings → Remote Play → Disable all Remote Play features

3. **Whisky configuration**
   - Enable DXVK in the Config tab (improves DirectX performance)
   - Enable Esync if available (improves multi-threading)

4. **Game-specific launch options**
   - Right-click on a game → Properties → Launch Options
   - Add for most games:
     ```
     DXVK_ASYNC=1 %command%
     ```

## Troubleshooting

### Steam won't launch or shows a blank window
- Try adding `-silent -nocrashmonitor -no-browser` to the launch arguments
- Install additional Winetricks components: wininet, msls31

### Specific error: "Unexpected Transport Error (0x3008)"
- Add `-no-browser -no-cef-sandbox` to launch arguments
- Set environment variables in Whisky Config:
  ```
  STEAM_RUNTIME=0
  STEAM_RUNTIME_HEAVY=0
  ```

### Games crash on launch
- Check if the game has specific requirements in the [Whisky wiki](https://github.com/Whisky-App/Whisky/wiki/Game-Support)
- Try adding `-force-d3d9` or `-force-opengl` to game launch options
- Right-click the game → Properties → set compatibility to Windows 7

### Performance issues
- Lower in-game resolution and graphics settings
- Disable post-processing effects
- Use the `-dx9` launch option for games that support it
- Close other resource-intensive applications

## Game-Specific Tips

### General compatibility
- 2D games generally work very well
- Indie games with simpler graphics tend to have fewer issues
- Games with OpenGL or DirectX 9/11 support work better
- Games with anti-cheat systems (EAC, BattlEye) usually don't work

### For demanding games like Elden Ring
- Set game-specific environment variables in Whisky:
  ```
  DXVK_ASYNC=1
  DXVK_FRAME_RATE=60
  ```
- Use the game's lowest graphics settings initially
- Reduce resolution to 1280x720 or lower for better performance
- Gradually increase settings once you confirm the game runs stably

---

## Credits

This guide was developed by testing various approaches from the Whisky community, particularly the approach shared by [neighborhoodciv on Nov 6, 2024](https://github.com/Whisky-App/Whisky/issues/94.

Special thanks to the Whisky development team for making Windows gaming on Apple Silicon possible!
