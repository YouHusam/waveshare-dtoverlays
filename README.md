## WaveShare SpotPear 3.2" and 3.5/4.0" LCD Device Tree Overlays for the Raspberry PI

This is Device Tree Overlays of [WaveShare SpotPear 3.2" TFT
LCD](http://www.waveshare.com/product/modules/oleds-lcds/3.2inch-rpi-lcd-b.htm)
and [WaveShare SpotPear 3.5" TFT
LCD](http://www.waveshare.com/product/modules/oleds-lcds/3.5inch-rpi-lcd-a.htm)
for the Raspberry PI and PI 2 using
[notro](https://github.com/notro)'s FBTFT driver.

Note that the waveshare 3.5/4.0" lcd's overlay is almost same with
[JBTek overlay](https://github.com/acidjazz/jbtekoverlay).

### Requirements

- GNU Coreutils
- GNU Make
- dtc (device tree compiler)

### Installation

1.) Clone this repo onto your pi
```shell
git clone https://github.com/swkim01/waveshare-dtoverlays.git
cd waveshare-dtoverlays
```

2.) Compile the device tree overlay binary objects:
```shell
make
```

3.) Install the device tree overlay binary files to /boot/overlays using:
```shell
sudo make install
```
4.) Reboot your raspberry pi

### Rotation

By default, the display is oriented in portrait mode, with its top
toward its connectors (on the opposite side of the Raspberry Pi USB
connectors). To rotate it, you can use the `rotate` dtoverlay
parameter, which causes the display to rotate counter-clockwise.  If
you use the touchscreen, you will also want to rotate the touchscreen
axes as well, by using the `invertx`, `inverty` and `swapxy`
parameters.

For example, if you want the display in landscape mode with its top
toward your Raspberry Pi HMDI connector, you can use:

```
dtoverlay=waveshare35b-v2,rotate=90,inverty=1,swapxy=1
```

To have the screen in landscape mode with its bottom toward your
Raspberry Pi HDMI connector, you can use:

```
dtoverlay=waveshare35b-v2,rotate=270,invertx=1,swapxy=1
```

### Touch Calibration

To calibrate in detail, you can build a modified source of
[xinput-calibrator](https://github.com/kreijack/xinput_calibrator/tree/libinput)

```
$ sudo apt install xinput-calibrator
$ export DISPLAY=:0
$ xinput_calibrator
```
Configure xinput_calibrator to autostart with X windows.

```
$ sudo cp -a scripts/xinput_calibrator_pointercal.sh /etc/X11/Xsession.d
$ echo "sudo /bin/sh /etc/X11/Xsession.d/xinput_calibrator_pointercal.sh" \
  | sudo tee -a /etc/xdg/lxsession/LXDE-pi/autostart
```
On first start of X windows a calibration window will be displayed.

```
startx
```

After calibration, the calibration file `/etc/pointercal.xinput` will
be created automatically.  Or instead you can create
`/usr/share/X11/xorg.conf.d/99-calibration.conf` by executing
`xinput_calibrator` or copy from `/etc/pointercal.xinput`.

```
Section "InputClass"
        Identifier "calibration"
        MatchProduct "ADS7846 Touchscreen"
        Option "TransformationMatrix" "0.016152 -1.137751 1.062519 1.126908 -0.005470 -0.064818 0.0 0.0 1.0"
EndSection
```

Calibrate the matrix with xinupt set-prop
Adjust the Transformation Matrix: This 3x3 matrix maps input coordinates to screen coordinates. The format is c0 0 c1 0 c2 c3 0 0 1.

- c0 and c2 control X and Y scaling.
- c1 and c3 control X and Y offset (translation).
    
```
xinput list
xinput list-props "ADS7846 Touchscreen"
xinput set-prop "ADS7846 Touchscreen" "Coordinate Transformation Matrix" 1.13 0 -0.1 0 1.11 -0.04 0 0 1
```


in `/boot/firmware/config.txt` add the following to the bottom:

```
[all]
dtparam=audio=on
dtoverlay=waveshare32b,rotate=90,swapxy=1,speed=22222222
hdmi_force_hotplug=1
#max_usb_current=1
hdmi_group=2
hdmi_mode=1
hdmi_mode=87
hdmi_cvt 320 240 60 1 0 0 0
hdmi_drive=2

dtoverlay=pi3-miniuart-bt
gpu_mem=256
```
