# TJURM-2024

**Beiyang RoboMaster 2024 Auto-Aiming Framework**



---

**This framework requires the vision algorithm library [OpenRM](https://github.com/HHgzs/OpenRM-2024) to work**


**Welcome to check the Wiki for more information [TJURM Auto-Aiming Algorithm Wiki](https://github.com/HHgzs/TJURM-2024/wiki/TJURM%E8%87%AA%E7%9E%84%E7%AE%97%E6%B3%95Wiki)**


## 🌟 Code Highlights 🌟



### 🎖️ Full Lineup

Integrates **auto-aiming, rune striking, and outpost attacking** in one, compatible with all robot types including **infantry, sentry, hero, and drone**

**Bilibili: [【RM Auto-Aim】Beiyang RoboMaster 2024 Season Auto-Aim Open Source!](https://www.bilibili.com/video/BV1jApUe1EPT/)** 





### 🚀 Pipeline Architecture

This auto-aiming system adopts a pipeline architecture, ensuring program throughput even when GPU processing time is high

The main pipeline infers **v5n** network, achieving **180fps** on **Nvidia NX Xavier** devices and **300fps** on **Nvidia AGX Orin** devices





### 🙌 Algorithm and Framework Separation



**Algorithm Library**

- The algorithm library uses dynamic link library format, making it easy to adapt to more vision projects and facilitating code reuse

- https://github.com/HHgzs/OpenRM-2024



**Auto-Aiming Framework**

- The auto-aiming framework has no specific algorithm implementation. By calling algorithms implemented in the algorithm library, it builds a pipeline architecture to achieve high efficiency and high extensibility

- https://github.com/HHgzs/TJURM-2024





### 🔌 Easy-to-Extend Models

Supports neural network output formats used by most teams, can support model inputs of any size, only needs parameter modification to implement



**V5**

- Standard yolov5 output format, i.e. **(x + y + w + h) + conf + n * classes**
- The class item can include color or not

**FP**

- Four-point format, i.e. **(x0 + y0 + x1 + y1 + x2 + y2 + x3 + y3) + conf + n * classes**
- The class item can include color or not

**FPX**

- Four-point format with color and ID separated
- **(x0 + y0 + x1 + y1 + x2 + y2 + x3 + y3) + conf + m * color_classes + n * id_classes**





## 🧬 Basic Features 🧬

- Computing Device: **Nvidia NX Xavier**
- Industrial Camera: **Daheng USB3.0**
- Recommended Resolution: **1280x1024**
- Recommended Lens: **6mm (cmos 1/2.9)**


## 🤖 Design Patterns 🤖

- **Factory Pattern**: Vehicle management module, generates different vehicle instances based on configuration files
- **Strategy Pattern**: Framework algorithm separation, different modules can be selected according to parameters
- **Observer Pattern**: Serial communication module, determines auto-aiming mode and main pipeline based on serial control
- **Pipeline Pattern**: Pipeline architecture for efficient real-time processing
- **Singleton Pattern**: Parameter management module, ensures parameter uniqueness
- **Template Method Pattern**: Template data structures, template motion models


## 🦺 Environment Setup 🦺



### Compile and Install OpenRM

First, you need to configure the OpenRM library. For details, see the repository README. Please visit the OpenRM-2024 repository page and don't forget to star it ⭐️

https://github.com/HHgzs/OpenRM-2024





### Compile and Install TJURM-2024

After successfully compiling and installing the OpenRM dynamic link library, the environment dependencies for TJURM-2024 are basically installed



Run `run.sh` to automatically execute compilation and installation

```bash
cd TJURM-2024
sudo ./run.sh
```



After installation is complete, `<--- Run Code --->` will be displayed, and the code will officially start running

```bash
[ 93%] Linking CXX static library libthreads.a
[ 93%] Built target threads
[ 96%] Building CXX object CMakeFiles/TJURM-2024.dir/src/main.cpp.o
[100%] Linking CXX executable TJURM-2024
[100%] Built target TJURM-2024

<--- Total Lines --->
        4110

<--- Run Code --->
```



Since this auto-aiming repository is shared by all robot types, to avoid conflicts between different robot types caused by parameter modifications, and to avoid frequent repository updates

Parameter information under `TJURM-2024/data/uniconfig` will be installed to `/etc/openrm`, and a symbolic link named `TJURM-2024/config` will be created to the `TJURM-2024` code directory

At this time, by accessing and modifying the parameters in `TJURM-2024/config`, you can directly change the parameters in the system, which will be called directly when the code runs next time



### Auto-Start

Use **rc.local** to implement auto-start

First create **rc.local**

```bash
vim /etc/rc.local
```



Add content to **rc.local**, don't forget the **&**

```bash
#!/bin/bash

sleep 5
sudo /etc/openrm/guard.sh &
```



Add execution permissions

```bash
chmod 755 /etc/rc.local
```



Use the **rc-local** service

```bash
# Start service
systemctl start rc-local.service
 
# Restart service
systemctl restart rc-local 

# Show status
systemctl status rc-local.service
```







### Image Output

If you want to enable the `imshow` function, you need to add the `-s` command at runtime.



- You can run `sudo ./run.sh -s` in the project folder `TJURM-2024`

  - This method will call the `guard.sh` guard script to ensure the code restarts immediately if terminated during execution


- You can run `TJURM-2024 -s` from any directory

  - Since the program is installed in `\usr\local\bin`, this method works globally
  - This method only calls one run, does not enable the guard script






### Parameter Display

In the OpenRM auto-aiming algorithm library, a terminal parameter panel based on `ncurses` has been implemented. For details, see the **OpenRM-2024** repository

https://github.com/HHgzs/OpenRM-2024



#### Enable Parameter Panel

```bash
openrm -d
```



**💥Note**

- If there is no openrm program, please check whether the step of installing this parameter panel program was ignored during OpenRM installation
- For details, see the `README` of **OpenRM-2024**   https://github.com/HHgzs/OpenRM-2024





#### Enable Oscilloscope

On the left side of the parameter panel, there are tuples of parameter names and parameter values. If you need to display one or several parameters in the form of an oscilloscope, you can use the oscilloscope function provided by `openrm`



For example, for the parameter `target omg`, for parameters with spaces, you can use escape characters

```bash
openrm -o target\ omg
```

Or use quotes

```bash
openrm -o "target omg"
```



Up to four waveforms can be displayed simultaneously

```bash
openrm -o "target -x-" "target -y-" "target -z-" "target omg"
```











## 📚️ File Introduction 📚️

```bash
.
├── CMakeLists.txt                   # CMake file
├── config -> /etc/openrm            # Symbolic link for modifying parameters
├── data
│   ├── debug                        # Debug function, frame-by-frame image storage
│   ├── speed                        # Projectile speed record file storage location
│   ├── uniconfig                    # Unified parameters, can force overwrite system parameters via sudo ./run.sh -c
│   │   ├── CamLens.json             # Camera parameters
│   │   ├── Config.json              # Main parameters, detailed introduction below
│   │   ├── guard.sh                 # Guard script
│   │   ├── image                    # Reprojection mask
│   │   └── models                   # ONNX models go here
│   └── video                        # Match recording storage location
│
├── include
│   ├── data_manager
│   │   ├── base.h                   # Basic global parameters
│   │   └── param.h                  # Call JSON to read parameters
│   ├── garage
│   │   ├── garage.h                 # Garage management header file
│   │   ├── interface.h              # Parent class
│   │   ├── wrapper_car.h            # Standard armor plate vehicle derived class
│   │   ├── wrapper_rune.h           # Rune striking derived class
│   │   └── wrapper_tower.h          # Outpost base derived class
│   │
│   └── threads
│       ├── control
│       │   ├── crc.h                # CRC verification part
│       │   └── structure.h          # Communication structure with lower computer
│       │
│       ├── control.h                # Control module header file
│       └── pipeline.h               # Pipeline header file
│
├── libs
│   └── json
│       
├── README.md
├── run.sh
│
└── src
    ├── data_manager
    │   ├── base.cpp
    │   ├── CMakeLists.txt
    │   ├── init.cpp
    │   └── param.cpp
    ├── garage
    │   ├── CMakeLists.txt
    │   ├── garage.cpp
    │   ├── wrapper_car.cpp
    │   ├── wrapper_rune.cpp
    │   └── wrapper_tower.cpp
    │
    ├── main.cpp    # Main function, starts threads and suspends itself
    │
    └── threads
        ├── CMakeLists.txt
        ├── control
        │   ├── crc.cpp
        │   ├── receive_thread.cpp               # Serial port reception logic processing
        │   └── send_thread.cpp                  # Call target prediction, ballistics calculation and control sending
        ├── control.cpp                          # Basic send/receive functions
        ├── pipeline
        │   ├── baseline                         # Auto-aim main pipeline, uses standard YOLO rectangles
        │   │   ├── preprocessor_thread.cpp      # Pipeline stage 1, neural network preprocessing and pushing
        │   │   ├── detector_thread.cpp          # Pipeline stage 2, synchronous inference and NMS
        │   │   ├── tracker                      
        │   │   │   ├── pointer.cpp              # Find four points
        │   │   │   ├── locater.cpp              # Pose estimation
        │   │   │   └── updater.cpp              # Model update
        │   │   └── tracker_thread.cpp           # Pipeline stage 3, motion model update
        │   │
        │   ├── fourpoints                       # Auto-aim four-point pipeline, uses four-point network
        │   │   ├── preprocessor_thread.cpp      # Pipeline stage 1, preprocessing
        │   │   ├── detector_thread.cpp          # Pipeline stage 2, synchronous inference and NMS
        │   │   └── fourpoints.cpp               # Pipeline stage 3, motion model update
        │   │
        │   ├── rune                             # Rune striking four-point pipeline
        │   │   ├── preprocessor_thread.cpp      # Pipeline stage 1, preprocessing
        │   │   ├── detector_thread.cpp          # Pipeline stage 2, synchronous inference and NMS
        │   │   └── tracker_thread.cpp           # Pipeline stage 3, rune model update
        │   │
        │   ├── image_thread.cpp                 # Image display and debug
        │   ├── recording_thread.cpp             # Recording thread
        │   └── ui.cpp                           # UI thread
        │
        └── pipeline.cpp                         # Pipeline startup function
```





## 🎄 Parameter Introduction 🎄



This mainly introduces the parameter functions of the **Debug** section. For other parts, if you don't understand, you can contact the author



```json
"Debug" : {
        "System" : {
            "AutoFire" : true,            // Automatic firing
            "AutoEnemy" : true,           // Enemy detection based on color provided by lower computer, blue 0, red 1
            "AutoRune" : true,            // Rune striking controlled by lower computer config
            "AutoCapture" : false         // Recording in non-auto-aiming state
        },
        "ImageThread" : {
            "Scale" : 0.5,                // imshow zoom size
            "FPS" : 25,                   // imshow frame rate
            "UI" : true,                  // Enable UI for imshow
            "Imwrite" : false,            // Save images frame by frame for debugging
            "Binary" : false,             // Binary image display
            "Light" : false,              // Brighten imshow
            "Histogram" : false           // Armor plate histogram display
        },
        "Display" : {
            "Reprojection" : false,       // Armor plate sticker reprojection, requires imshow to be enabled
            "PipelineDelay" : false,      // Delay for each stage of pipeline
            "PointSkip" : false           // Print light bar filtering conditions
        },
        "Control" : {
            "Serial" : true,              // Enable lower computer serial communication thread
            "Timeout" : true,             // Thread timeout auto-termination
            "ManuCapture" : false,        // Manual forced recording
            "ManuFire" : false,           // Manual firing
            "ManuRune" : false,           // Manual entry into rune mode, for debugging only
            "BigRune" : false             // Entering rune defaults to small rune, big rune when enabled
        },
        "PlusPnP" : {
            "Enable" : true,              // PnP method proposed by SJTU, let's thank them
            "CostImage" : false           // Loss function image, requires imshow to be enabled
        },
        "StateDelay" : {                  // Alternative to synchronized triggering
            "Enable" : true,              
            "TimeS" : 0.05,
            "QueueSize" : 100,
            "SendWait" : 10
        },
        "SaveSpeed" : {                   // Hero projectile speed register
            "SpeedWrite" : true,
            "SavePath" : "/etc/openrm/speed_log.txt"
        },

        "BigDecal" : "/etc/openrm/image/armor1.png",        // Reprojection large armor image
        "SmallDecal" : "/etc/openrm/image/armor3.png"       // Reprojection small armor image
    }
```

