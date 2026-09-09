# esp32
it is related to programming the esp32 microcontroller
To run the ESP-IDF extension within WSL (Windows Subsystem for Linux), you need a split setup: the VS Code user interface runs on Windows, while the entire ESP-IDF toolchain, compiler, and Python environment reside inside your Linux distro (like Ubuntu).
Because you are using WSL, you also need to handle how Windows talks to the physical USB port where your ESP32 is plugged in.
## Step 1: Install Linux Prerequisites inside WSL
Open your WSL terminal and run the following commands to install the required Linux packages and dependencies:

sudo apt-get update
sudo apt-get install -y git wget flex bison gperf python3 python3-pip python3-venv cmake ninja-build ccache libffi-dev libssl-dev dfu-util libusb-1.0-0

## Step 2: Install the WSL Extension in VS Code

   1. Open VS Code on Windows.
   2. Install the official WSL extension by Microsoft from the Extensions Marketplace.
   3. Open your WSL environment inside VS Code. You can do this by clicking the green remote icon in the bottom-left corner of VS Code and selecting Connect to WSL.

## Step 3: Install the ESP-IDF Extension Inside WSL

   1. Once VS Code is connected to your WSL instance, open the Extensions Marketplace (Ctrl+Shift+X).
   2. Search for ESP-IDF.
   3. You will see a button that says Install in WSL: [Your Distro Name]. Click it.

## Step 4: Run the ESP-IDF Configuration Wizard

   1. Open the Command Palette inside your WSL VS Code window (Ctrl+Shift+P).
   2. Type ESP-IDF: Configure ESP-IDF Extension and select it.
   3. Choose Express Setup.
   4. Set your ESP-IDF directory paths (e.g., /home/username/esp/esp-idf). Make sure there are no spaces in the path.
   5. Click Install. The extension will now download the Linux binaries for the compiler and tools directly into your WSL file system.

------------------------------
## Critical Step for WSL: USB Port Forwarding (usbipd)
WSL cannot natively see USB devices plugged into your Windows machine. To flash your ESP32 board, you must forward the USB port from Windows to WSL using a tool called usbipd-win.

   1. On Windows (PowerShell as Administrator): Download and install the latest .msi from the [usbipd-win GitHub repository](https://github.com/dorssel/usbipd-win).
   2. On WSL (Ubuntu Terminal): Install the Linux USB tools:
   
   sudo apt install linux-tools-virtual hwdata
   sudo update-alternatives --install /usr/local/bin/usbipd usbipd /usr/lib/linux-tools/*/usbipd 100
   
   3. Plug in your ESP32 to your PC's USB port.
   4. On Windows (PowerShell): Find the bus ID of your ESP32:
   
   usbipd list
   
   (Look for something named "Silicon Labs CP210x" or "CH340" and note its Bus ID, e.g., 2-3).
   5. On Windows (PowerShell): Attach it to WSL:
   
   usbipd attach --busid 2-3 --wsl
   
   6. On WSL: Verify the device is visible by typing ls /dev/ttyUSB* or ls /dev/ttyACM*. You should see something like /dev/ttyUSB0.

Now, inside your VS Code window at the bottom status bar, you can select /dev/ttyUSB0 as your serial port, click Build, and click Flash!
To make sure everything connects smoothly, let me know:

* Which Linux distro are you running in WSL (e.g., Ubuntu 22.04, Ubuntu 24.04)?
* What ESP32 chip variant are you using (e.g., standard ESP32, S3, C3)?
* Are you encountering any permission denied errors when trying to read the /dev/ttyUSB0 port in WSL?


