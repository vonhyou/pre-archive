# USRA Weekly Meeting: May. 24, 2023

## Setup Environment

First time running:

Details: [Root AVDs Without Play Store](https://academic.sfeng.ca/blog/capture-avd-traffic-androiddump#root-avds-without-play-store)

```mermaid
flowchart LR
  Start --> R[Root the device] --> I[Install TCPdump] --> Finish
  R --> W[Install Androiddump in Wireshark] --> Finish
```

Future running:

Details: [Postprocessing](https://academic.sfeng.ca/blog/capture-avd-traffic-androiddump#postprocessing)

```mermaid
flowchart LR
  Start --> B[Boot AVD in commandline] --> W[Run Wireshark] --> Finish
```

## TikTok

### Original Process

The Android client does not have an autoplay function, so I write a [script](https://github.com/vonhyou/usra23-archive/blob/master/Scripts/auto_swipe.ps1) to swipe to the next video after a random time interval. 

```mermaid
flowchart LR
  Start --> Running

  subgraph Start
    RunWireshark[Start Capturing] --> 
    RunTikTok[Start Tiktok] --> 
    RunScript["Run auto-swipe script
    e.g. `.\auto_swipe.ps1 59`"]
  end

  subgraph Running
    Play[Playing video] -->
    isTime{{Reach the time limit?}} --> |Y| Finish
    isTime --> |"N, play next video 
    for [6, 120] seconds"| Play
  end
```

### Revised Process

I will observe and capture traffic during the login process

```mermaid
flowchart LR
  Start --> Running

  subgraph Start
    RunWireshark[Start Capturing] --> 
    RunTikTok[Start Tiktok] --> 
    Login[Login TikTok]:::new -->
    RunScript["Run auto-swipe script
    e.g. `.\auto_swipe.ps1 59`"]
  end

  subgraph Running
    Play[Playing video] -->
    isTime{{Reach the time limit?}} --> |Y| Finish
    isTime --> |"N, play next video 
    for [6, 120] seconds"| Play
  end

  classDef new fill:#f96
```

### Google Chrome

At the very beginning, I plan to open and close URLs using `adb`. However, google chrome does not have a certain [`intent`](https://developer.android.com/reference/android/content/Intent) to close a tab (I can open, but cannot close), and no function to close all tabs when exiting. Therefore, I implement a [webpage](https://github.com/vonhyou/usra23-archive/tree/master/Web/open-close) using HTML/JavaScript to automate the process.


```mermaid
flowchart LR
  Start --> Running

  subgraph Start
    RunWebpage[Open the webpage] -->
    Load["Load URL list and 
    generate time intervals"] -->
    RunWireshark[Start Captureing]
  end

  subgraph Running
    Open[Open the ith URL] -->
    |Wait for the ith time interval| TurnOff[Close the URL] -->
    isTime{{Reach the time limit?}} --> |Y| Finish
    isTime --> |"N"| Open
  end
```

For the time interval, see: [SelectTimeIntervals.m](https://github.com/vonhyou/usra23-archive/blob/master/Matlab/SelectTimeIntervals/SelectTimeIntervals.m)

Sample output:

![](https://github.com/vonhyou/usra23-archive/raw/master/Matlab/SelectTimeIntervals/SelectTimeIntervals.jpg)

**Waiting for further instructions**

### Google Drive

