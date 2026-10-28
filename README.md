# ScreenCalib

The full source code will be released once the associated paper is published or this repository receives strong community interest.

Use a display to quickly calibrate LiDAR–camera extrinsics.

[Watch the full ScreenCalib software demo](https://www.youtube.com/watch?v=ckqXlYIHabs)

[Watch the single-pose (SP) mode demo](https://youtu.be/TpmN8ugaYNQ)

## Download

The [1.1.0 release](https://github.com/ZuhaoZou2026/ScreenCalib/releases/tag/1.1.0) provides the native GUI for **Ubuntu 18.04 x86_64**, including Single-Pose and Joint Multi-Pose modes.

| Download | Contents |
| --- | --- |
| [ScreenCalib-1.1.0-ubuntu18.04-x86_64.tar.gz](https://github.com/ZuhaoZou2026/ScreenCalib/releases/download/1.1.0/ScreenCalib-1.1.0-ubuntu18.04-x86_64.tar.gz) | GUI and bundled runtime |
| [ScreenCalib-1.1.0-ubuntu18.04-x86_64.sha256](https://github.com/ZuhaoZou2026/ScreenCalib/releases/download/1.1.0/ScreenCalib-1.1.0-ubuntu18.04-x86_64.sha256) | SHA-256 checksum for the GUI archive |
| [MP_gui_example_bag.zip](https://github.com/ZuhaoZou2026/ScreenCalib/releases/download/1.1.0/MP_gui_example_bag.zip) | Multi-pose example ROS bag and matching camera-intrinsics YAML |

## Install and launch

Download the GUI archive and checksum into the same directory, then run:

```bash
sha256sum -c ScreenCalib-1.1.0-ubuntu18.04-x86_64.sha256
tar -xzf ScreenCalib-1.1.0-ubuntu18.04-x86_64.tar.gz
cd ScreenCalib-Ubuntu18-x86_64
python3 refresh-launcher.py .
./ScreenCalib.sh
```

Run as your normal user, without `sudo`. To launch by icon, double-click `ScreenCalib.desktop` and select **Allow Launching** if prompted. Keep the extracted folder together in a writable location.

The package includes its matching Linux userspace, Python, ROS, OpenCV, PCL, Ceres, Tk, Pillow, and PRoot. Running it requires no separate ROS, Python packages, Docker, or administrator access. It requires an x86_64 Linux host with a graphical X11 desktop and permission to use `ptrace`. Input files under `/home`, `/media`, and `/mnt` are accessible. Results are saved in the extracted package's `results/` directory.

Check the bundled runtime with:

```bash
./ScreenCalib.sh --check
```

## Try the multi-pose example

Extract `MP_gui_example_bag.zip`. It contains:

- `velodyne64_sx_0p0_sy_m0p0_sz_m0p0.bag`
- `sim_velodyne64_camera_from_bag.yaml`

The ground-truth (GT) extrinsic transform is included in the example bag's `/tf` topic.

1. Open ScreenCalib and choose **Joint Multi-Pose Mode**.
2. Select the extracted bag and camera YAML, and enter the AprilTag black-square size in meters used in the recording.
3. Click **Load bag** and select the camera topic matching the YAML: `/sim/camera/image_raw`. Select the bag's LiDAR topic where prompted.
4. Click **Cut into static poses**. Review the motion plot and detected intervals. Adjust the motion threshold and click **Cut again** if needed.
5. Click **Calibrate** to process the static-pose bags. Use the **Pose N** buttons to inspect individual results.
6. When at least two successful pose bundles are available, click **Joint calibration**. Review convergence and geometry checks before using the final extrinsic transform.

## Single-pose workflow

Choose **Single-Pose Mode**, load a ROS 1 bag, select camera and LiDAR topics, and click **Check bag**. After validation, click **Next**, enter the AprilTag black-square size in meters, select the camera-intrinsics YAML and output directory, then click **Run calibration**.

The [v1.0.0 release](https://github.com/ZuhaoZou2026/ScreenCalib/releases/tag/v1.0.0) includes the **Single-Pose (SP) mode GUI** and [SP_gui_example_bag.zip](https://github.com/ZuhaoZou2026/ScreenCalib/releases/download/v1.0.0/SP_gui_example_bag.zip), containing the SP example bag and matching camera-intrinsics YAML.
