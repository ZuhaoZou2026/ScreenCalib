# ScreenCalib
Use a display to quickly calibrate LiDAR-camera extrinsic

[Watch the ScreenCalib GUI demo](media/ScreenCalib_SP_GUI.mp4)

For Ubuntu 18.04 x86_64, download [the native package](https://github.com/ZuhaoZou2026/ScreenCalib/releases/download/v1.0.0/ScreenCalib-1.0.0-ubuntu18.04-x86_64.tar.gz), extract it, and run `bash install.sh` inside the extracted folder as your normal user (without `sudo`). The installer works offline, includes the required runtime libraries, and creates a ScreenCalib desktop shortcut; no separate ROS, Python packages, or Docker installation is needed. Open ScreenCalib, load a ROS 1 bag, select the camera and LiDAR topics, and click **Check bag**. After validation, click **Next**, enter the AprilTag black-square size in meters, select your camera-intrinsics YAML and output directory, then click **Run calibration**.
