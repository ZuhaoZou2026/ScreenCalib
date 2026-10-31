# ScreenCalib

The full source code will be released once the associated paper is published or this repository receives strong community interest.

Use a display to quickly calibrate LiDAR–camera extrinsics.

[Watch the full ScreenCalib software demo](https://www.youtube.com/watch?v=ckqXlYIHabs)

[Watch the single-pose (SP) mode demo](https://youtu.be/TpmN8ugaYNQ)

## Download

The [1.1.0 release](https://github.com/ZuhaoZou2026/ScreenCalib/releases/tag/1.1.0) provides native GUI packages for **Ubuntu 18.04 x86_64** and **Windows 11 x64**, including Single-Pose and Joint Multi-Pose modes.

| Download | Contents |
| --- | --- |
| [ScreenCalib-1.1.0-ubuntu18.04-x86_64.tar.gz](https://github.com/ZuhaoZou2026/ScreenCalib/releases/download/1.1.0/ScreenCalib-1.1.0-ubuntu18.04-x86_64.tar.gz) | GUI and bundled runtime |
| [ScreenCalib-1.1.0-ubuntu18.04-x86_64.sha256](https://github.com/ZuhaoZou2026/ScreenCalib/releases/download/1.1.0/ScreenCalib-1.1.0-ubuntu18.04-x86_64.sha256) | SHA-256 checksum for the GUI archive |
| [ScreenCalib-1.1.0-Windows11-x64.zip](https://github.com/ZuhaoZou2026/ScreenCalib/releases/download/1.1.0/ScreenCalib-1.1.0-Windows11-x64.zip) | Windows GUI and bundled runtime |
| [ScreenCalib-1.1.0-Windows11-x64.zip.sha256](https://github.com/ZuhaoZou2026/ScreenCalib/releases/download/1.1.0/ScreenCalib-1.1.0-Windows11-x64.zip.sha256) | Windows archive SHA-256 checksum |
| [ScreenCalib-1.1.0-Windows11-x64.zip.validation.json](https://github.com/ZuhaoZou2026/ScreenCalib/releases/download/1.1.0/ScreenCalib-1.1.0-Windows11-x64.zip.validation.json) | Windows validation scope and results |
| [MP_gui_example_bag.zip](https://github.com/ZuhaoZou2026/ScreenCalib/releases/download/1.1.0/MP_gui_example_bag.zip) | Multi-pose example ROS bag and matching camera-intrinsics YAML |

## Install and launch

### Ubuntu 18.04 x86_64

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

### Windows 11 x64

Download `ScreenCalib-1.1.0-Windows11-x64.zip` and fully extract it into a writable directory. Double-click **ScreenCalib.exe** inside the extracted folder. The offline package includes its runtime and third-party libraries; no project source, `.windows-build` directory, Python, Conda, ROS, Visual Studio, Docker, WSL, Bash, or online dependency downloads are required.

Keep the entire extracted folder together and leave it in the same location after the first launch. To move the application, extract the original ZIP again at the new location. The accompanying `.sha256` and `.validation.json` files document the archive checksum and validation scope. You can calculate the ZIP checksum in PowerShell and compare it with the supplied checksum:

```powershell
Get-FileHash .\ScreenCalib-1.1.0-Windows11-x64.zip -Algorithm SHA256
```

The Windows package passed dependency-loading checks, the native launcher's self-check, GUI startup, and a complete single-pose Steps 1–11 run using `pose_1.bag` with a tag size of **0.0558 m** and **300 ICP iterations**. This tag size applies to that validation recording; measure the tag size for your own data. The run also exported a joint-optimization bundle. The dependency audit found loaded DLLs only in the package or Windows directories, with the Visual C++ runtime loaded from the package.

A clean Windows 11 Sandbox test was not completed because the Sandbox session could not start. Full 12-pose joint calibration, numerical comparison with Ubuntu, and all GUI interactions throughout a complete run have not been verified.

The standalone PCL/VTK viewer is disabled by default on Windows. ScreenCalib's Python 3D views and ICP pose stream remain available. When invoking a backend manually, use `--headless 1`; requesting the disabled standalone viewer produces an error.

<details>
<summary>Windows development build and validation details</summary>

These instructions describe the development checkout, rather than the offline release package. They require the corresponding source files once available.

The seven backends were built with Visual Studio 2022 / MSVC 19.40 and Windows SDK 10.0.22621. The development runtime is stored in `.windows-build/windows-eigen34/environment`, and installed backend files are in `dist/ScreenCalib-Windows11-x64-eigen34/bin`.

In a development checkout, launch **ScreenCalib-Windows.cmd** from the project root. It configures Python, ROS modules, DLLs, and backend search paths. Keep `.windows-build/windows-eigen34/environment` available. A launcher in an intermediate build directory without a packaged runtime is not a standalone application; use the project-root `.cmd` for that development setup.

To rebuild using an existing project environment, open PowerShell in the project root:

```powershell
powershell -NoProfile -ExecutionPolicy Bypass -File src/alcc/release/windows/build-windows.ps1 -BuildOnly
```

This builds, tests, and installs the backends. For a first build on another machine, install Visual Studio 2022 with Desktop development with C++ and Miniforge, then specify `-CondaExecutable <full-path-to-conda.exe>`. Dependencies are defined in `src/alcc/release/windows/dependencies-win64.lock.txt`. Omitting `-BuildOnly` attempts runtime ZIP packaging; choose an `-Output` path that does not already exist and validate the resulting package before distributing it.

| Component | Windows implementation |
| --- | --- |
| ROS 1 / rosbag / cv_bridge | Native RoboStack Windows packages for bag reading |
| Livox | Generated CustomMsg/CustomPoint messages without building hardware drivers |
| OpenCV | 4.5.5 with the vc16 CMake configuration, used with MSVC 19.40 |
| PCL | 1.12.0; point-cloud computation retained, standalone PCL/VTK viewers disabled by default |
| Ceres / Eigen / Boost | Native libraries: 2.2.0 / 3.4.0 / 1.74.0 |
| AprilTag | Repository's ethz_apriltag2 implementation, without building all of Kalibr |
| Pipeline orchestration | `run_native_screen_calib.py` on Windows; original entry point retained on Linux |
| Process cancellation | `taskkill` terminates the process tree |
| TIFF / libdeflate | libdeflate pinned to 1.14 to satisfy the older TIFF dependency |

Gazebo, the full Kalibr project, and unrelated ROS nodes are not built. Eigen 3.4.0 and the matching Ceres build use `EIGEN_MALLOC_ALREADY_ALIGNED=0` to match precompiled PCL's allocation behavior. The previous Eigen 5 environment caused `0xC0000374` heap corruption when releasing DLL-allocated point clouds; the replacement environment uses a separate directory and preserves previous environments and results.

Reported checks passed: two C++ algorithm tests, a PCL memory-lifecycle test, 48 GUI unit tests, five native-pipeline helper tests, seven backend help/DLL-loading checks, and 13 Windows joint-optimization integration tests. Two tests specific to the old Linux Bash scheduler were explicitly skipped on Windows. The GUI was also created and entered its event loop.

Development logs and outputs:

- `.windows-build/native-pose1-verify.log`
- `.windows-build/native-pose1-verify/`
- `.windows-build/eigen34-build.log`
- `.windows-build/windows-eigen34/build/Testing/Temporary/LastTest.log`

</details>

## Prepare the display

Available target images (choose one):

- [apriltag_black.png](apriltag_black.png)
- [apriltag_black_rectangle.png](apriltag_black_rectangle.png)
- [apriltag_gray_rectangle.png](apriltag_gray_rectangle.png)
- [apriltag_white_rectangle.png](apriltag_white_rectangle.png)

Before recording your own calibration bag, choose one `apriltag_xx.png` image and show it on the display used as the calibration target.

Use a ruler to measure the actual side length of one AprilTag's black square on the screen, excluding the surrounding white margin. Convert the measurement to meters and enter it as the AprilTag size in the GUI (for example, 35 mm = 0.035 m). Measure with the image at the same display scale used during recording; remeasure if you resize the image or change the display scaling.

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
