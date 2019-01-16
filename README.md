# ros2_openvino_toolkit_issues
This is a public repository about ros2_openvino_toolkit issues

# Issues and solution
- run sample with Intel® Neural Compute Stick 2. OpenVINO binary R4 + opencv v3.4.2
```bash
E: [ncAPI] [         0] ncDeviceOpen:668	failed to find device
#or
E: [ncAPI] [         0] ncDeviceCreate:324      global mutex initialization failed
```
- solution
```bash
reboot
```
- gtk conflict error
```bash
Gtk-ERROR **: GTK+ 2.x symbols detected. Using GTK+ 2.x and GTK+ 3 in the same process is not supported
```
I have tried to use ldd to show the runtime usage of libs in my exes.
```bash
ldd pipeline_with_params | grep opencv
	libopencv_highgui.so.4.0 => /opt/intel_r4//computer_vision_sdk_2018.4.420/opencv/lib/libopencv_highgui.so.4.0 (0x00007fef86bf0000)
	libopencv_videoio.so.4.0 => /opt/intel_r4//computer_vision_sdk_2018.4.420/opencv/lib/libopencv_videoio.so.4.0 (0x00007fef869aa000)
	libopencv_highgui.so.3.4 => /usr/local/lib/libopencv_highgui.so.3.4 (0x00007fef8229c000)
	libopencv_videoio.so.3.4 => /usr/local/lib/libopencv_videoio.so.3.4 (0x00007fef82048000)
	libopencv_imgcodecs.so.3.4 => /usr/local/lib/libopencv_imgcodecs.so.3.4 (0x00007fef81a61000)
	libopencv_imgproc.so.3.4 => /usr/local/lib/libopencv_imgproc.so.3.4 (0x00007fef7f14f000)
	libopencv_core.so.3.4 => /usr/local/lib/libopencv_core.so.3.4 (0x00007fef7e238000)
	libopencv_imgcodecs.so.4.0 => /opt/intel_r4//computer_vision_sdk_2018.4.420/opencv/lib/libopencv_imgcodecs.so.4.0 (0x00007fef7c1d7000)
	libopencv_imgproc.so.4.0 => /opt/intel_r4//computer_vision_sdk_2018.4.420/opencv/lib/libopencv_imgproc.so.4.0 (0x00007fef7654b000)
	libopencv_core.so.4.0 => /opt/intel_r4//computer_vision_sdk_2018.4.420/opencv/lib/libopencv_core.so.4.0 (0x00007fef74a23000)

ldd pipeline_with_params | grep gtk
	libgtk-x11-2.0.so.0 => /usr/lib/x86_64-linux-gnu/libgtk-x11-2.0.so.0 (0x00007f24a3fc2000)
	libgtk-3.so.0 => /usr/lib/x86_64-linux-gnu/libgtk-3.so.0 (0x00007f2497489000)

readelf -d /opt/intel_r4//computer_vision_sdk_2018.4.420/opencv/lib/libopencv_highgui.so.4.0 | grep library
 0x0000000000000001 (NEEDED)             Shared library: [libgtk-x11-2.0.so.0]
 0x0000000000000001 (NEEDED)             Shared library: [libgdk-x11-2.0.so.0]
 0x0000000000000001 (NEEDED)             Shared library: [libcairo.so.2]
 0x0000000000000001 (NEEDED)             Shared library: [libgdk_pixbuf-2.0.so.0]
 0x0000000000000001 (NEEDED)             Shared library: [libgobject-2.0.so.0]
 0x0000000000000001 (NEEDED)             Shared library: [libglib-2.0.so.0]
 0x0000000000000001 (NEEDED)             Shared library: [libopencv_imgcodecs.so.4.0]
 0x0000000000000001 (NEEDED)             Shared library: [libopencv_imgproc.so.4.0]
 0x0000000000000001 (NEEDED)             Shared library: [libopencv_core.so.4.0]
 0x0000000000000001 (NEEDED)             Shared library: [libstdc++.so.6]
 0x0000000000000001 (NEEDED)             Shared library: [libgcc_s.so.1]
 0x0000000000000001 (NEEDED)             Shared library: [libc.so.6]

readelf -d /usr/local/lib/libopencv_highgui.so.3.4 | grep library
 0x0000000000000001 (NEEDED)             Shared library: [libgtk-3.so.0]
 0x0000000000000001 (NEEDED)             Shared library: [libgdk-3.so.0]
 0x0000000000000001 (NEEDED)             Shared library: [libcairo.so.2]
 0x0000000000000001 (NEEDED)             Shared library: [libgdk_pixbuf-2.0.so.0]
 0x0000000000000001 (NEEDED)             Shared library: [libgobject-2.0.so.0]
 0x0000000000000001 (NEEDED)             Shared library: [libglib-2.0.so.0]
 0x0000000000000001 (NEEDED)             Shared library: [libopencv_imgcodecs.so.3.4]
 0x0000000000000001 (NEEDED)             Shared library: [libopencv_imgproc.so.3.4]
 0x0000000000000001 (NEEDED)             Shared library: [libopencv_core.so.3.4]
 0x0000000000000001 (NEEDED)             Shared library: [libstdc++.so.6]
 0x0000000000000001 (NEEDED)             Shared library: [libgcc_s.so.1]
 0x0000000000000001 (NEEDED)             Shared library: [libc.so.6]
```
From the above results, we can see why there is a gtk conflict error. OpenVINO R4 integrated opencv v4.0 depends on gtk+2.x, but opencv v3.4.2 depends on gtk+3.0
- solution
Nedd to modify CMake to specify opencv v3.4.2
```bash
set(OpenCV_DIR $HOME/code/opencv/build)
```

```bash
intel@intel-desktop:~/ros2_overlay_ws/install/dynamic_vino_sample/lib/dynamic_vino_sample$ ldd pipeline_with_params | grep opencv
	libopencv_core.so.3.4 => /usr/local/lib/libopencv_core.so.3.4 (0x00007f5f16646000)
	libopencv_highgui.so.3.4 => /usr/local/lib/libopencv_highgui.so.3.4 (0x00007f5f11f38000)
	libopencv_videoio.so.3.4 => /usr/local/lib/libopencv_videoio.so.3.4 (0x00007f5f11ce4000)
	libopencv_imgcodecs.so.3.4 => /usr/local/lib/libopencv_imgcodecs.so.3.4 (0x00007f5f116fd000)
	libopencv_imgproc.so.3.4 => /usr/local/lib/libopencv_imgproc.so.3.4 (0x00007f5f0edeb000)
```
