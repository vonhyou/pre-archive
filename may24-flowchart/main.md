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

Initially, I plan to open and close URLs using `adb`. However, google chrome does not have a certain [`intent`](https://developer.android.com/reference/android/content/Intent) to close a tab (I can open but cannot close), and no function to close all tabs when exiting. Therefore, I implement a [webpage](https://github.com/vonhyou/usra23-archive/tree/master/Web/open-close) using HTML/JavaScript to automate the process.


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

I did not automate the uploading and downloading process because of its proprietary software nature. Moreover, I have restricted the uploading speed to 10Mbps and downloading to 40Mbps. Therefore, limited user operation is required.

```mermaid
flowchart LR
  Start --> Running

  subgraph Start
    RunGoogleDrive[Run Google drive] -->
    RunWireshark[Start Captureing]
  end

  subgraph Running
    Upload[Select file/files to upload/download] -->
    isTime{{Reach the time limit?}} --> |Y| Finish
    isTime --> |"N"| Upload
  end
```

My current issue is that I cannot upload a large archive (2GB), so I’ll adjust some of the files to the paper [10.1109/ICCISci.2019.8716385](https://ieeexplore.ieee.org/document/8716385). The final dataset is going to be:

```
photo-small: 3MB in total, 792 pictures
photo: 102MB in total, 17 pictures
photo-large: 112MB in total, 2 pictures

archive.zip: about 200MB
archive-large.zip: about 1000MB
```

The behaviour of uploading and downloading many small files is quite interesting. In the first round, uploading all 3MB photos takes longer than a 224MB single file.



**Waiting for further instructions**
