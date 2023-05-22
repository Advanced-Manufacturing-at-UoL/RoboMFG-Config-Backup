# RoboMFG-Config-Backup
Backup for the robot manufacturing platforms Klipper firmware configuration

# Updating Toolboards
To update the 2 toolboards currently assigned to the FFF toolheads SSH into the pi and run the following commands.
```
cp -f /home/pi/printer_data/config/RoboMFG/toolboards/firmware/fff_head_1/firmware.config /home/pi/klipper/.config
pushd /home/pi/klipper
make olddefconfig
make clean
make
pushd /home/pi/klipper
service klipper stop
make flash FLASH_DEVICE=/dev/serial/by-id/usb-Klipper_stm32g0b1xx_fff_head_1-if00
chown pi:pi -R /home/pi/klipper
service klipper start
cp -f /home/pi/printer_data/config/RoboMFG/toolboards/firmware/fff_head_2/firmware.config /home/pi/klipper/.config
pushd /home/pi/klipper
make olddefconfig
make clean
make
pushd /home/pi/klipper
service klipper stop
make flash FLASH_DEVICE=/dev/serial/by-id/usb-Klipper_stm32g0b1xx_fff_head_2-if00
chown pi:pi -R /home/pi/klipper
service klipper start
```

If after updating, the version of Klipper does not match that of the pi it could be due to changes to how by-id and by-path mapping in systemd works in the latest Debian update. This is a known issue and can be resolved by running:
```
sudo sed -i '/^SUBSYSTEMS=="pci", ENV{ID_BUS}="pci".*/i SUBSYSTEMS=="usb", IMPORT{builtin}="usb_id", IMPORT{builtin}="hwdb --subsystem=usb"' /lib/udev/rules.d/60-serial.rules 
sudo sed -i 's/^SUBSYSTEMS=="pci", ENV{ID_BUS}="pci".*/SUBSYSTEMS=="pci", ENV{ID_BUS}=="", ENV{ID_BUS}="pci", \\\
  ENV{ID_VENDOR_ID}="$attr{vendor}", ENV{ID_MODEL_ID}="$attr{device}", \\\
  IMPORT{builtin}="hwdb --subsystem=pci"/g' /lib/udev/rules.d/60-serial.rules 
sudo sed -i '/ENV{MODALIAS}==".*/d' /lib/udev/rules.d/60-serial.rules
sudo reboot now
```
