# OpenClaw installation commands for Raspberry Pi (Debian 12 Bookworm, arm64)

> Target host from your report:
> - Debian 12 (bookworm)
> - Linux aarch64
> - Enough free disk space available

## 1) Update system

```bash
sudo apt update
sudo apt full-upgrade -y
sudo apt autoremove -y
```

## 2) Install required build/runtime packages

```bash
sudo apt install -y \
  git cmake ninja-build build-essential pkg-config \
  libsdl2-dev libsdl2-image-dev libsdl2-mixer-dev libsdl2-ttf-dev \
  libopenal-dev libogg-dev libvorbis-dev zlib1g-dev libpng-dev \
  libglew-dev libglm-dev libcurl4-openssl-dev \
  xorg xinit mesa-utils
```

## 3) Clone OpenClaw source

```bash
cd /home/thomas
git clone --recursive https://github.com/pjasicek/OpenClaw.git
cd OpenClaw
```

If you already cloned it before:

```bash
cd /home/thomas/OpenClaw
git pull --rebase
git submodule update --init --recursive
```

## 4) Build OpenClaw

```bash
cd /home/thomas/OpenClaw
cmake -S . -B build -G Ninja -DCMAKE_BUILD_TYPE=Release
cmake --build build -j"$(nproc)"
```

## 5) Install OpenClaw binary

```bash
sudo cmake --install build --prefix /usr/local
sudo ldconfig
```

## 6) Add game data files (required)

OpenClaw executable needs original game data files (PAK/RES assets) from your legal copy of Captain Claw.

Create data folder and copy files:

```bash
sudo mkdir -p /usr/local/share/OpenClaw
sudo cp -av /path/to/your/CLAW_DATA/* /usr/local/share/OpenClaw/
```

(Replace `/path/to/your/CLAW_DATA` with the folder that contains the game resource files.)

## 7) Run OpenClaw

Try these commands (depends on how binary is installed by upstream CMake):

```bash
openclaw
```

or

```bash
/usr/local/bin/OpenClaw
```

If data path is not auto-detected, run with explicit path:

```bash
/usr/local/bin/OpenClaw --data-path /usr/local/share/OpenClaw
```

## 8) Optional: create desktop launcher

```bash
cat <<'DESKTOP' | sudo tee /usr/share/applications/openclaw.desktop
[Desktop Entry]
Type=Application
Name=OpenClaw
Exec=/usr/local/bin/OpenClaw --data-path /usr/local/share/OpenClaw
Icon=applications-games
Categories=Game;
Terminal=false
DESKTOP
```

## 9) Quick troubleshooting commands

Check binary linkage:

```bash
ldd /usr/local/bin/OpenClaw
```

Check OpenGL/Vulkan stack:

```bash
glxinfo | head -n 20
```

Run with logs:

```bash
/usr/local/bin/OpenClaw --data-path /usr/local/share/OpenClaw 2>&1 | tee ~/openclaw.log
```
