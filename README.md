# Samsung Galaxy A03 ROM Builder

This repo is used to build custom ROMs for the Samsung Galaxy A03 (SM-A035F), it can also be used to build for other devices just make sure you know what you're doing.

## Quick Start

### Using the Automated Workflow

1. **Navigate to Actions tab** in your forked repository
2. **Click on "ROM Builder For Samsung Galaxy A03"** workflow
3. **Click "Run workflow"** button
4. **Fill in the parameters**:
    - **GSI Image Download Link**: Direct download link to your GSI (supports .xz, .tar.xz, .zip, .7z formats)
    - **ROM Name**: Output filename (e.g., `VoltageOS-arm64-ab-microg-15.0-a03.7z`)
    - **Baseband Version**: Your device's baseband version (find in Settings > About phone)
    - **Vendor Image Download Link**: Direct download link to vendor.img for your baseband

5. **Click "Run workflow"** and wait for completion
6. **Download** the built ROM from the Releases section

### Example Parameters

```
GSI: https://github.com/cawilliamson/treble_voltage/releases/download/4.5-20250717.114637/VoltageOS-microg-arm64-ab-4.5-20250717.114637-UNOFFICIAL.img.xz
ROM Name: VoltageOS-arm64-ab-microg-15.0-a03.7z
Baseband Version: A035FXXF8CYA3
Vendor: https://sourceforge.net/projects/a03-files/files/A035FXXU4CWI1/vendor.img
```

## Local Build

### Prerequisites

#### Install Dependencies

**Ubuntu/Debian:**

```bash
sudo apt update
sudo apt install -y git wget curl unzip zip p7zip-full xz-utils atool build-essential
```

**Arch Linux:**

```bash
sudo pacman -S git wget curl unzip zip p7zip xz atool base-devel
```

**CentOS/RHEL/Fedora:**

```bash
# Fedora
sudo dnf install -y git wget curl unzip zip p7zip xz atool gcc make

# CentOS/RHEL (enable EPEL first)
sudo yum install -y epel-release
sudo yum install -y git wget curl unzip zip p7zip xz atool gcc make
```

### Manual Build Process

#### 1. Setup Build Environment

```bash
# Clone LP tools
git clone https://github.com/Exynos-nigg/lpunpack-lpmake-mirror.git lpbinary
chmod +x lpbinary/binary/lpmake

# Put your system_ext.img, vendor.img, and product.img in here
cd lpbinary/binary
```

**NOTE**: if you don't know where to find these files like vendor or system_ext images you can just extract the super.img of your device with `7z`

#### 2. Create Super Image

```bash
# Get file sizes for lpmake
SYSTEM_SIZE=$(stat -c%s system.img)
VENDOR_SIZE=$(stat -c%s vendor.img)
PRODUCT_SIZE=$(stat -c%s product.img)
SYSTEM_EXT_SIZE=$(stat -c%s system_ext.img)

./lpmake --metadata-size 65536 \
         --super-name super \
         --metadata-slots 2 \
         --device super:6763315200 \
         --group main:6761218048 \
         --partition system:readonly:${SYSTEM_SIZE}:main \
         --image system=system.img \
         --partition vendor:readonly:${VENDOR_SIZE}:main \
         --image vendor=vendor.img \
         --partition product:readonly:${PRODUCT_SIZE}:main \
         --image product=product.img \
         --partition system_ext:readonly:${SYSTEM_EXT_SIZE}:main \
         --image system_ext=system_ext.img \
         --sparse \
         --output super.img
```

**NOTE**: the numbers like `6763315200` and `6761218048` are devices specific so make sure to change them if you have other devices. You can get them by running `adb shell blockdev --getsize64 /dev/block/by-name/super` or just `blockdev --getsize64 /dev/block/by-name/super` in termux if you have root access.

## Credits

- [Johx22](https://github.com/Johx22/) - for the patch recovery script
- [antyrix](https://github.com/antyrix/) - for the original idea and inspiration
