# uvc-camera

Two small desktop apps that do the same job — preview a **UVC (USB Video Class)** camera, switch device and mode, and capture what you see. Same functionality, two stacks; pick whichever fits the machine you are on.

| Implementation | Stack | What you get |
| --- | --- | --- |
| [`python/`](python/) | Python 3 · OpenCV · tkinter | Runs straight from source, no build step. Switch camera and resolution, take a photo. |
| [`qt/`](qt/) | C++ · Qt 5 · QtMultimedia | A packaged desktop app. Adds frame-rate selection, photo capture **and video recording**. |

Both enumerate the cameras attached to the machine, let you switch between them, preview live at the selected resolution, and save the capture into your home directory as `capture.png` (the Qt build also writes `video.*` when recording). Each has its own `debian/` directory, so either one can be built into a `.deb` on its own.

---

## python/

**Requirements:** Python 3.8+, OpenCV 4.2+, tkinter, Pillow, and a UVC device that is already plugged in.

```bash
sudo apt install python3-tk python3-opencv python3-pil python3-pil.imagetk
python3 python/uvc-camera.py
```

Choose the camera and the resolution from the dropdowns, then hit **Take a picture**. The frame lands in your home directory as `capture.png` — only one is kept, a new one overwrites it. The resolution list is probed from the device at startup, so the options you see are the ones the camera actually reports.

[▶ Demo video (mp4)](https://user-images.githubusercontent.com/62707482/220237668-675d3b83-0b3e-449e-bf50-202973abc7dd.mp4)

**Build the Debian package:**

```bash
cd python && dpkg-buildpackage -b -us -uc
```

## qt/

**Requirements:** Qt 5 development packages and `qmake` (`qtbase5-dev`, `qtmultimedia5-dev`, `libqt5multimedia5-plugins`), plus the GStreamer bits listed in `qt/debian/control`.

```bash
cd qt
qmake openqtcamera-rebion.pro
make -j"$(nproc)"
./uvc-camera/uvc-camera
```

Frame-rate selection and video recording are what this build adds over the Python one. Photo and video both go to your home directory (`capture.png`, `video.*`), one of each.

**Build the Debian package:**

```bash
cd qt && dpkg-buildpackage -b -us -uc
```

---

## Notes

- Develops and runs on Ubuntu 20.04 (the Python build was verified against python3.8 + OpenCV 4.2).
- Cameras are discovered at startup, so connect the device before launching. If none is found the app reports it and exits instead of sitting on a dead preview.
- The two implementations are independent; there is no shared code between them.

## 中文

同一个功能的两个实现：`python/` 是 Python + OpenCV + tkinter 的轻量版，直接从源码跑，支持切换摄像头、切换分辨率、拍照；`qt/` 是 C++ / Qt5 的完整版，多了帧率切换和录像。两者都会自动列出已接入的摄像头并实时预览，拍照结果保存在用户家目录的 `capture.png`（Qt 版录像存 `video.*`，均只保留最新一份）。各自带 `debian/` 目录，可以分别打成 deb 包。

## License

MIT — see [LICENSE](LICENSE).
