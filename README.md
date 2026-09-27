# ESP32 Wi-Fi Penetration Testing Tool

An ESP32-based wireless security lab tool for capturing WPA/WPA2 handshakes and PMKIDs, exporting captures, and demonstrating Wi-Fi management-frame weaknesses.

> **Authorized use only:** Use this project only on networks and devices you own or have explicit permission to test. Deauthentication and denial-of-service features disrupt wireless connections and may be illegal when used without authorization.

This repository packages the source and prebuilt firmware from [risinek/esp32-wifi-penetration-tool](https://github.com/risinek/esp32-wifi-penetration-tool), together with a simplified flashing guide.

## Features

- PMKID capture
- WPA/WPA2 handshake capture and parsing
- Passive handshake sniffing
- Controlled deauthentication and denial-of-service demonstrations
- Rogue access-point testing
- PCAP export
- HCCAPX export for offline analysis
- Browser-based management interface
- Reusable ESP-IDF components for wireless research

Password cracking is not performed on the ESP32.

## Demo

- [Upstream demonstration video](https://www.youtube.com/watch?v=9I3BxRu86GE)
- Personal deployment demonstration: **Coming soon**

## Hardware requirements

- ESP32-DEVKITC-32E or a compatible ESP32-WROOM-32 development board
- USB data cable
- A controlled Wi-Fi lab network that you are authorized to test

## Quick start with the prebuilt firmware

### 1. Install the tools

Install a current Python 3 release, then install Espressif's `esptool`:

```bash
python -m pip install --upgrade esptool
```

If the ESP32 serial port does not appear, install the official Silicon Labs CP210x USB-to-UART VCP driver for your operating system. Driver installers are not stored in this repository.

### 2. Identify the serial port

Examples:

- Windows: `COM3`, `COM4`, or another port shown in Device Manager
- Linux: `/dev/ttyUSB0` or `/dev/ttyACM0`

Replace the example port in the command below with the port used by your ESP32.

### 3. Flash the firmware

Run the command from the repository root.

#### Windows

```powershell
python -m esptool --port COM3 --baud 115200 --after hard_reset write_flash --flash_mode dio --flash_freq 40m --flash_size detect 0x8000 build/partition_table/partition-table.bin 0x1000 build/bootloader/bootloader.bin 0x10000 build/esp32-wifi-penetration-tool.bin
```

#### Linux

```bash
python3 -m esptool --port /dev/ttyUSB0 --baud 115200 --after hard_reset write_flash --flash_mode dio --flash_freq 40m --flash_size detect 0x8000 build/partition_table/partition-table.bin 0x1000 build/bootloader/bootloader.bin 0x10000 build/esp32-wifi-penetration-tool.bin
```

Windows users can alternatively use Espressif's official [Flash Download Tool](https://www.espressif.com/en/support/download/other-tools) with the same three binaries and offsets.

| Offset | Firmware file |
| --- | --- |
| `0x1000` | `build/bootloader/bootloader.bin` |
| `0x8000` | `build/partition_table/partition-table.bin` |
| `0x10000` | `build/esp32-wifi-penetration-tool.bin` |

### 4. Open the management interface

After the ESP32 restarts:

1. Connect to the Wi-Fi network `ManagementAP`.
2. Enter the default passphrase `mgmtadmin`.
3. Open [http://192.168.4.1/](http://192.168.4.1/) in a browser.
4. Use the interface only with an authorized lab target.

## Build from source

The upstream project was developed with **ESP-IDF 4.1** at commit `5ef1b390026270503634ac3ec9f1ec2e364e23b2`. Newer ESP-IDF versions may require changes.

With the compatible ESP-IDF environment active:

```bash
idf.py build
idf.py flash
```

The legacy `make` workflow is not supported.

## Documentation

- [Attack implementation overview](main/README.md)
- [Wi-Fi attack theory](doc/ATTACKS_THEORY.md)
- [Wi-Fi controller component](components/wifi_controller/README.md)
- [Webserver component](components/webserver/README.md)
- [Frame analyzer component](components/frame_analyzer/README.md)
- [PCAP serializer component](components/pcap_serializer/README.md)
- [HCCAPX serializer component](components/hccapx_serializer/README.md)
- [Wi-Fi stack library bypasser](components/wsl_bypasser/README.md)

## Troubleshooting

### Serial port is missing

- Use a USB cable that supports data.
- Try another USB port.
- Install the correct CP210x driver if your board uses a Silicon Labs USB-to-UART bridge.

### Flashing remains at `Connecting...`

- Hold the ESP32 **BOOT** button while the connection starts.
- Release it after the upload begins.
- Confirm that no serial monitor is using the same port.

### Linux permission error

Add your user to the serial-port group used by your distribution, reconnect the board, and sign in again. Do not run untrusted flashing tools as an administrator merely to bypass permissions.

### Management page does not open

- Confirm that the device is connected to `ManagementAP`.
- Disable mobile data temporarily if the phone routes browser traffic away from the ESP32.
- Open `http://192.168.4.1/` explicitly, not HTTPS.

## Repository contents

```text
build/        Prebuilt bootloader, partition table, and application firmware
components/   Reusable ESP-IDF components
doc/          Architecture, theory, diagrams, and images
main/         Application entry point and attack implementations
LICENSE       MIT license from the upstream project
README.md     Deployment and usage guide
```

## Credits and license

Original project: [risinek/esp32-wifi-penetration-tool](https://github.com/risinek/esp32-wifi-penetration-tool)

The upstream project is licensed under the MIT License. The original copyright and license notice are preserved in [`LICENSE`](LICENSE).
