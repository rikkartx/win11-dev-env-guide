# Windows11 22H2 Installation (en)

## Updating your system first and reboot

## Uninstall unnecessary Apps

## (Optional) Install 7z from offical website

> If you need a proxy software like Clash, and only can get a 7z file.

Download 7z.exe from offical website, but we will uninstall this later (because we will use scoop to install 7z later).

## (Optional) Download Clash

## Install scoop in Windows Terminal

Open a PowerShell terminal (version 5.1 or later) and run:

``` ps
# Optional: Needed to run a remote script the first time
Set-ExecutionPolicy RemoteSigned -Scope CurrentUser

irm get.scoop.sh | iex
```

### Install 7z using scoop

``` ps
scoop install 7z
```

### Install git using scoop

``` ps
scoop install git
```

### Install sudo using scoop

``` ps
scoop install sudo
```

### Install Delugia Nerd Font (aka Caskaydia Cove)

``` ps
# add bucket
scoop bucket add nerd-fonts

# install fonts
sudo scoop install Delugia-Nerd-Font-Complete

# install mono fonts
sudo scoop install Delugia-Mono-Nerd-Font-Complete
```

## Download Dracula Pro Theme

## Configure Windows Terminal

Defaults -> Appearance

- `Font Face` -> Delugia
- `Color schema` -> Dracula Pro (read install.md in Dracula Pro)

## Install WSL2

> If you use proxy app or other use LSP app, you need disable LSP module for WSL2.

### (Optional) Disable LSP for WSL2

Save this script to a `.ps1` file.

``` ps
#Requires -RunAsAdministrator
# Fix for https://github.com/microsoft/WSL/issues/4177

$MethodDefinition = @'
[DllImport("ws2_32.dll", CharSet = CharSet.Unicode)]
public static extern int WSCSetApplicationCategory([MarshalAs(UnmanagedType.LPWStr)] string Path, uint PathLength, [MarshalAs(UnmanagedType.LPWStr)] string Extra, uint ExtraLength, uint PermittedLspCategories, out uint pPrevPermLspCat, out int lpErrno);
'@

$Ws2Spi = Add-Type -MemberDefinition $MethodDefinition -Name 'Ws2Spi' -PassThru

$WslLocation = Get-AppxPackage MicrosoftCorporationII.WindowsSubsystemForLinux | Select-Object -expand InstallLocation

$Executables = ("wsl.exe", "wslservice.exe");

foreach ($Exe in $Executables) {
    $ExePath = "${WslLocation}\${Exe}";
    $ExePathLength = $ExePath.Length;

    $PrevCat = $null;
    $ErrNo = $null;
    if ($Ws2Spi::WSCSetApplicationCategory($ExePath, $ExePathLength, $null, 0, [uint32]"0x80000000", [ref] $PrevCat, [ref] $ErrNo) -eq 0) {
        Write-Output "Added $ExePath!";
    }
}
```

Create a schdule in `Task Schduler`, exec this `ps1` file.

### Install

Run command in powershell

``` ps
#You can run this command to list linux distributions.
wsl -l -o

# install default distribute
wsl --install
```

After installing WSL2, you need reboot.

### Configure WSL2

Run `apt update` first.

``` bash
sudo apt update
```

#### Install ZSH

``` bash
# Install zsh
sudo apt install zsh

# Change default shell
chsh -s $(which zsh)
```

#### Install oh-my-zsh

``` bash
# Install oh-my-zsh
sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
```

#### Install spaceship theme of oh-my-zsh

``` bash
# Clone
git clone https://github.com/spaceship-prompt/spaceship-prompt.git "$ZSH_CUSTOM/themes/spaceship-prompt" --depth=1

# Symlink
ln -s "$ZSH_CUSTOM/themes/spaceship-prompt/spaceship.zsh-theme" "$ZSH_CUSTOM/themes/spaceship.zsh-theme"

# Set ZSH_THEME="spaceship" in your .zshrc

# Source zshrc
source .zshrc

# Create spaceship configuration file
touch ~/.spaceshiprc.zsh

# Edit .spaceshiprc.zsh

# Source
source .spaceshiprc.zsh
```

Here is a spaceship confiration file example.

``` ini
# This file is spaceship prompt cofiguration

# Not display Async Section
SPACESHIP_ASYNC_SHOW=false

# Display time
SPACESHIP_TIME_SHOW=true

# Display username always
SPACESHIP_USER_SHOW=always
```

#### Install zsh-syntax-highlighting plugin

``` bash
# Clone
git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting

# Set plugins=( [plugins...] zsh-syntax-highlighting) in .zshrc
```

#### (Optional) Install podman

[Install on Ubuntu](https://podman.io/docs/installation#ubuntu)

``` bash
sudo mkdir -p /etc/apt/keyrings

curl -fsSL "https://download.opensuse.org/repositories/devel:kubic:libcontainers:unstable/xUbuntu_$(lsb_release -rs)/Release.key" | gpg --dearmor | sudo tee /etc/apt/keyrings/devel_kubic_libcontainers_unstable.gpg > /dev/null

# WSL2 can't run lsb_release -rs, so need replace this to ubuntu version
echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/devel_kubic_libcontainers_unstable.gpg] https://download.opensuse.org/repositories/devel:kubic:libcontainers:unstable/xUbuntu_22.04/ /" | sudo tee /etc/apt/sources.list.d/devel:kubic:libcontainers:unstable.list > /dev/null

sudo apt update

sudo apt install podman
```

We can alias `docker='podman'` in `.zshrc`

``` bash
alias docker='podman'
```

#### (Optional) Install Podman Desktop

Thanks to WSLg we can run gui app in wsl2.

1. Install flatpak first.

    ``` bash
    sudo add-apt-repository ppa:flatpak/stable

    sudo apt update

    sudo apt install flatpak
    ```

    or just

    ``` bash
    sudo apt install flatpak
    ```

2. Install `org.freedesktop.Platform`

    ``` bash
    flatpak install -u org.freedesktop.Platform
    ```

3. Install `Podman Desktop`

    [Install Podman Desktop use Flatpak](https://podman-desktop.io/docs/Installation/linux-install/installing-podman-desktop-from-a-flatpak-bundle#procedure)

    Download `podman-desktop-<version>.flatpak` manully and install.

    ``` bash
    flatpak install -u $where_you_download_file_store
    ```

4. Run `Podman Desktop`

    ``` bash
    # Set alias in .zshrc
    alias podman-desktop='flatpak run -u io.podman_desktop.PodmanDesktop'

    # Run
    podman-desktop
    ```

#### (Optional) Install Docker

Follow the guide [Docke Engine Install](https://docs.docker.com/engine/install/)

## Install Chrome

Change to Dracula Pro theme.

## Install Vitual Studio Code (User)

> Install to `%LocalAppData%\Programs`

- Change `Font` -> 'Delugia', 'Microsoft YaHei UI'
- Change `Color schema` -> Dracula Pro (read install.md in Dracula Pro)

### Install extensions

- [ ] [Markdown Checkbox](https://marketplace.visualstudio.com/items?itemName=bierner.markdown-checkbox)
- [ ] [Markdown Emoji](https://marketplace.visualstudio.com/items?itemName=bierner.markdown-emoji)
- [ ] [Markdown Preview Mermaid Support](https://marketplace.visualstudio.com/items?itemName=bierner.markdown-mermaid)
- [ ] [markdownlint](https://marketplace.visualstudio.com/items?itemName=DavidAnson.vscode-markdownlint)
- [ ] [C/C++ Extension Pack](https://marketplace.visualstudio.com/items?itemName=ms-vscode.cpptools-extension-pack)
- [ ] [ES7+ React/Redux/React-Native snippets](https://marketplace.visualstudio.com/items?itemName=dsznajder.es7-react-js-snippets)
- [ ] [Markdown PDF](https://marketplace.visualstudio.com/items?itemName=yzane.markdown-pdf)
- [ ] [GitLens](https://marketplace.visualstudio.com/items?itemName=eamodio.gitlens)
- [ ] [Hex Editor](https://marketplace.visualstudio.com/items?itemName=ms-vscode.hexeditor)
- [ ] [Prettier](https://marketplace.visualstudio.com/items?itemName=esbenp.prettier-vscode)
- [ ] [rust-analyzer](https://marketplace.visualstudio.com/items?itemName=rust-lang.rust-analyzer)
- [ ] [Remote Development](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.vscode-remote-extensionpack)
- [ ] [Encode Decode](https://marketplace.visualstudio.com/items?itemName=mitchdenny.ecdc)
- [ ] [URI Encode/Decode](https://marketplace.visualstudio.com/items?itemName=sryze.uridecode)
- [ ] [Auto Complete Tag](https://marketplace.visualstudio.com/items?itemName=formulahendry.auto-complete-tag)

### About use Remote SSH offline

[Solution](https://stackoverflow.com/questions/56671520/how-can-i-install-vscode-server-in-linux-offline/57601121#57601121)

> For version 1.82.0

1. You need download [vscode-server-linux-x64](https://update.code.visualstudio.com/latest/server-linux-x64/stable) or [vscode-server-linux-arm64](https://update.code.visualstudio.com/latest/server-linux-arm64/stable). Change `latest` in url to `commit:${COMMIT_ID}`, get `${COMMIT_ID}` from vscode app `Help -> About -> Commit`.

    ``` text
    Version: 1.82.0 (user setup)
    Commit: 8b617bd08fd9e3fc94d14adb8d358b56e3f72314
    Date: 2023-09-06T22:07:07.438Z
    Electron: 25.8.0
    ElectronBuildId: 23503258
    Chromium: 114.0.5735.289
    Node.js: 18.15.0
    V8: 11.4.183.29-electron.0
    OS: Windows_NT x64 10.0.22621
    ```

2. Transfer `vscode-server-linux-${ARCH}.tar.gz` to remote.
3. Extract all content in `vscode-server-linux-${ARCH}.tar.gz` to `~/.vscode-server/bin/${COMMIT_ID}`.
4. Create `0` file under `~/.vscode-server/bin/${COMMIT_ID}`.

All steps convert to bash scripts will be like this:

``` bash
# Get this from vscode app `Help -> About`
COMMIT_ID=8b617bd08fd9e3fc94d14adb8d358b56e3f72314

# x64 or arm64
ARCH=x64

# Download vscode-server-linux-${ARCH}.tar.gz
curl -sSL "https://update.code.visualstudio.com/commit:${COMMIT_ID}/server-linux-${ARCH}/stable" -o vscode-server-linux-${ARCH}.tar.gz

# Create directory
mkdir -p ~/.vscode-server/bin/${COMMIT_ID}

# Extract content in tar to directory
tar zxvf vscode-server-linux-x64.tar.gz -C ~/.vscode-server/bin/${COMMIT_ID} --strip 1

# Create 0 file
touch ~/.vscode-server/bin/${COMMIT_ID}/0
```

## (Optional) Configure System FontLink

> Only if you use CJK font and you're not using Japanese for your primary languge.

1. `Win + R`, enter `regedit`
2. Find `HKEY_LOCAL_MACHINE/SOFTWARE/Microsoft/Windows NT/CurrentVersion/FontLink/SystemLink`
3. Backup key `Segoe UI`, `Segoe UI Bold`, `Segoe UI Light`
4. Change value and reboot

### Segoe UI

> For Chinese.

``` text
MSYH.TTC,Microsoft YaHei UI,128,96
MSYH.TTC,Microsoft YaHei UI
TAHOMA.TTF,Tahoma
MEIRYO.TTC,Meiryo UI,128,96
MEIRYO.TTC,Meiryo UI
MSGOTHIC.TTC,MS UI Gothic
MSJH.TTC,Microsoft JhengHei UI,128,96
MSJH.TTC,Microsoft JhengHei UI
MALGUN.TTF,Malgun Gothic,128,96
MALGUN.TTF,Malgun Gothic
MINGLIU.TTC,PMingLiU
SIMSUN.TTC,SimSun
GULIM.TTC,Gulim
YUGOTHM.TTC,Yu Gothic UI,128,96
YUGOTHM.TTC,Yu Gothic UI
DELUGIAMONOCOMPLETE.TTF,Delugia Mono
SEGUISYM.TTF,Segoe UI Symbol

```

### Segoe UI Bold

> For Chinese.

``` text
MSYHBD.TTC,Microsoft YaHei UI Bold,128,96
MSYHBD.TTC,Microsoft YaHei UI Bold
MEIRYOB.TTC,Meiryo UI Bold,128,96
MEIRYOB.TTC,Meiryo UI Bold
MSJHBD.TTC,Microsoft JhengHei UI Bold,128,96
MSJHBD.TTC,Microsoft JhengHei UI Bold
MALGUNBD.TTF,Malgun Gothic Bold,128,96
MALGUNBD.TTF,Malgun Gothic Bold
YUGOTHB.TTC,Yu Gothic UI Bold,128,96
YUGOTHB.TTC,Yu Gothic UI Bold
DELUGIAMONOCOMPLETE-BOLD.TTF,Delugia Mono Bold
SEGUISYM.TTF,Segoe UI Symbol

```

### Segoe UI Light

> For Chinese.

``` text
MSYHL.TTC,Microsoft YaHei UI Light,128,96
MSYHL.TTC,Microsoft YaHei UI Light
MEIRYO.TTC,Meiryo UI,128,96
MEIRYO.TTC,Meiryo UI
MSJHL.TTC,Microsoft JhengHei UI Light,128,96
MSJHL.TTC,Microsoft JhengHei UI Light
MALGUNSL.TTF,Malgun Gothic Semilight,128,96
MALGUNSL.TTF,Malgun Gothic Semilight
YUGOTHL.TTC,Yu Gothic UI Light,128,96
YUGOTHL.TTC,Yu Gothic UI Light
DELUGIAMONOCOMPLETELIGHT.TTF,Delugia Mono Light
SEGUISYM.TTF,Segoe UI Symbol

```

## For Javaer

## Install Temurin OpenJDK

``` ps
# Add java bucket
scoop bucket add java

# Search jdk
scoop search jdk

# Install jdk8
scoop install temurin8-jdk

# Install jdk11
scoop install temurin11-jdk

# Install jdk17
scoop install temurin17-jdk

# Install jdk20 (not work for now, maybe work after jdk21 release)
scoop install temurin20-jdk

# Install graalvm (jdk8)
scoop install graalvm-jdk8

# Install graalvm (jdk11)
scoop install graalvm22-jdk11

# Install graalvm (jdk17)
scoop install graalvm22-jdk17

# Install graalvm (jdk19 for now 2023.09.09)
scoop install graalvm
```

### Graalvm Native

If you want build your jar into native executable file, you need install msvc and windows sdk kits.

You can use this python scripts instead of isntalling Visutal Stuido Build Installer.

So you need install `python3` first.

``` ps
# install python3
scoop install python
```

And then run this script.

<details>

<summary>Too long, Click me</summary>

``` py
#!/usr/bin/env python3

import io
import json
import shutil
import hashlib
import zipfile
import tempfile
import argparse
import subprocess
import urllib.request
from pathlib import Path

OUTPUT = Path("msvc") # output folder
TEMP = Path(".").absolute()

# other architectures may work or may not - not really tested
HOST   = "x64" # or x86
TARGET = "x64" # or x86, arm, arm64

MANIFEST_URL = "https://aka.ms/vs/17/release/channel"


def download(url):
  with urllib.request.urlopen(url) as res:
    return res.read()

def download_progress(url, check, name, f):
  data = io.BytesIO()
  with urllib.request.urlopen(url) as res:
    total = int(res.headers["Content-Length"])
    size = 0
    while True:
      block = res.read(1<<20)
      if not block:
        break
      f.write(block)
      data.write(block)
      size += len(block)
      perc = size * 100 // total
      print(f"\r{name} ... {perc}%", end="")
  print()
  data = data.getvalue()
  digest = hashlib.sha256(data).hexdigest()
  if check.lower() != digest:
    exit(f"Hash mismatch for f{pkg}")
  return data

# super crappy msi format parser just to find required .cab files
def get_msi_cabs(msi):
  index = 0
  while True:
    index = msi.find(b".cab", index+4)
    if index < 0:
      return
    yield msi[index-32:index+4].decode("ascii")

def first(items, cond):
  return next(item for item in items if cond(item))


### parse command-line arguments

ap = argparse.ArgumentParser()
ap.add_argument("--show-versions", const=True, action="store_const", help="Show available MSVC and Windows SDK versions")
ap.add_argument("--accept-license", const=True, action="store_const", help="Automatically accept license")
ap.add_argument("--msvc-version", help="Get specific MSVC version")
ap.add_argument("--sdk-version", help="Get specific Windows SDK version")
args = ap.parse_args()


### get main manifest

manifest = json.loads(download(MANIFEST_URL))


### download VS manifest

vs = first(manifest["channelItems"], lambda x: x["id"] == "Microsoft.VisualStudio.Manifests.VisualStudio")
payload = vs["payloads"][0]["url"]

vsmanifest = json.loads(download(payload))


### find MSVC & WinSDK versions

packages = {}
for p in vsmanifest["packages"]:
  packages.setdefault(p["id"].lower(), []).append(p)

msvc = {}
sdk = {}

for pid,p in packages.items():
  if pid.startswith("Microsoft.VisualStudio.Component.VC.".lower()) and pid.endswith(".x86.x64".lower()):
    pver = ".".join(pid.split(".")[4:6])
    if pver[0].isnumeric():
      msvc[pver] = pid
  elif pid.startswith("Microsoft.VisualStudio.Component.Windows10SDK.".lower()) or \
       pid.startswith("Microsoft.VisualStudio.Component.Windows11SDK.".lower()):
    pver = pid.split(".")[-1]
    if pver.isnumeric():
      sdk[pver] = pid

if args.show_versions:
  print("MSVC versions:", " ".join(sorted(msvc.keys())))
  print("Windows SDK versions:", " ".join(sorted(sdk.keys())))
  exit(0)

msvc_ver = args.msvc_version or max(sorted(msvc.keys()))
sdk_ver = args.sdk_version or max(sorted(sdk.keys()))

if msvc_ver in msvc:
  msvc_pid = msvc[msvc_ver]
  msvc_ver = ".".join(msvc_pid.split(".")[4:-2])
else:
  exit(f"Unknown MSVC version: f{args.msvc_version}")

if sdk_ver in sdk:
  sdk_pid = sdk[sdk_ver]
else:
  exit(f"Unknown Windows SDK version: f{args.sdk_version}")

print(f"Downloading MSVC v{msvc_ver} and Windows SDK v{sdk_ver}")


### agree to license

tools = first(manifest["channelItems"], lambda x: x["id"] == "Microsoft.VisualStudio.Product.BuildTools")
resource = first(tools["localizedResources"], lambda x: x["language"] == "en-us")
license = resource["license"]

if not args.accept_license:
  accept = input(f"Do you accept Visual Studio license at {license} [Y/N] ? ")
  if not accept or accept[0].lower() != "y":
    exit(0)

OUTPUT.mkdir(exist_ok=True)
total_download = 0

### download MSVC

msvc_packages = [
  # MSVC binaries
  f"microsoft.vc.{msvc_ver}.tools.host{HOST}.target{TARGET}.base",
  f"microsoft.vc.{msvc_ver}.tools.host{HOST}.target{TARGET}.res.base",
  # MSVC headers
  f"microsoft.vc.{msvc_ver}.crt.headers.base",
  # MSVC libs
  f"microsoft.vc.{msvc_ver}.crt.{TARGET}.desktop.base",
  f"microsoft.vc.{msvc_ver}.crt.{TARGET}.store.base",
  # MSVC runtime source
  f"microsoft.vc.{msvc_ver}.crt.source.base",
  # ASAN
  f"microsoft.vc.{msvc_ver}.asan.headers.base",
  f"microsoft.vc.{msvc_ver}.asan.{TARGET}.base",
  # MSVC redist
  #f"microsoft.vc.{msvc_ver}.crt.redist.x64.base",
]

for pkg in msvc_packages:
  p = first(packages[pkg], lambda p: p.get("language") in (None, "en-US"))
  for payload in p["payloads"]:
    with tempfile.TemporaryFile(dir=TEMP) as f:
      data = download_progress(payload["url"], payload["sha256"], pkg, f)
      total_download += len(data)
      with zipfile.ZipFile(f) as z:
        for name in z.namelist():
          if name.startswith("Contents/"):
            out = OUTPUT / Path(name).relative_to("Contents")
            out.parent.mkdir(parents=True, exist_ok=True)
            out.write_bytes(z.read(name))


### download Windows SDK

sdk_packages = [
  # Windows SDK tools (like rc.exe & mt.exe)
  f"Windows SDK for Windows Store Apps Tools-x86_en-us.msi",
  # Windows SDK headers
  f"Windows SDK for Windows Store Apps Headers-x86_en-us.msi",
  f"Windows SDK Desktop Headers x86-x86_en-us.msi",
  # Windows SDK libs
  f"Windows SDK for Windows Store Apps Libs-x86_en-us.msi",
  f"Windows SDK Desktop Libs {TARGET}-x86_en-us.msi",
  # CRT headers & libs
  f"Universal CRT Headers Libraries and Sources-x86_en-us.msi",
  # CRT redist
  #"Universal CRT Redistributable-x86_en-us.msi",
]

with tempfile.TemporaryDirectory(dir=TEMP) as d:
  dst = Path(d)

  sdk_pkg = packages[sdk_pid][0]
  sdk_pkg = packages[first(sdk_pkg["dependencies"], lambda x: True).lower()][0]

  msi = []
  cabs = []

  # download msi files
  for pkg in sdk_packages:
    payload = first(sdk_pkg["payloads"], lambda p: p["fileName"] == f"Installers\\{pkg}")
    msi.append(dst / pkg)
    with open(dst / pkg, "wb") as f:
      data = download_progress(payload["url"], payload["sha256"], pkg, f)
      total_download += len(data)
      cabs += list(get_msi_cabs(data))

  # download .cab files
  for pkg in cabs:
    payload = first(sdk_pkg["payloads"], lambda p: p["fileName"] == f"Installers\\{pkg}")
    with open(dst / pkg, "wb") as f:
      download_progress(payload["url"], payload["sha256"], pkg, f)

  print("Unpacking msi files...")

  # run msi installers
  for m in msi:
    subprocess.check_call(["msiexec.exe", "/a", m, "/quiet", "/qn", f"TARGETDIR={OUTPUT.resolve()}"])


### versions

msvcv = list((OUTPUT / "VC/Tools/MSVC").glob("*"))[0].name
sdkv = list((OUTPUT / "Windows Kits/10/bin").glob("*"))[0].name


# place debug CRT runtime files into MSVC folder (not what real Visual Studio installer does... but is reasonable)

dst = OUTPUT / "VC/Tools/MSVC" / msvcv / f"bin/Host{HOST}/{TARGET}"

with tempfile.TemporaryDirectory(dir=TEMP) as d:
  d = Path(d)
  pkg = "microsoft.visualcpp.runtimedebug.14"
  dbg = first(packages[pkg], lambda p: p["chip"] == HOST)
  for payload in dbg["payloads"]:
    name = payload["fileName"]
    with open(d / name, "wb") as f:
      data = download_progress(payload["url"], payload["sha256"], f"{pkg}/{name}", f)
      total_download += len(data)
  msi = d / first(dbg["payloads"], lambda p: p["fileName"].endswith(".msi"))["fileName"]

  with tempfile.TemporaryDirectory(dir=TEMP) as d2:
    subprocess.check_call(["msiexec.exe", "/a", str(msi), "/quiet", "/qn", f"TARGETDIR={d2}"])
    for f in first(Path(d2).glob("System*"), lambda x: True).iterdir():
      f.replace(dst / f.name)

# download DIA SDK and put msdia140.dll file into MSVC folder

with tempfile.TemporaryDirectory(dir=TEMP) as d:
  d = Path(d)
  pkg = "microsoft.visualc.140.dia.sdk.msi"
  dia = packages[pkg][0]
  for payload in dia["payloads"]:
    name = payload["fileName"]
    with open(d / name, "wb") as f:
      data = download_progress(payload["url"], payload["sha256"], f"{pkg}/{name}", f)
      total_download += len(data)
  msi = d / first(dia["payloads"], lambda p: p["fileName"].endswith(".msi"))["fileName"]

  with tempfile.TemporaryDirectory(dir=TEMP) as d2:
    subprocess.check_call(["msiexec.exe", "/a", str(msi), "/quiet", "/qn", f"TARGETDIR={d2}"])

    if HOST == "x86": msdia = "msdia140.dll"
    elif HOST == "x64": msdia = "amd64/msdia140.dll"
    else: exit("unknown")

    src = Path(d2) / "Program Files" / "Microsoft Visual Studio 14.0" / "DIA SDK" / "bin" / msdia
    src.replace(dst / "msdia140.dll")


### cleanup

shutil.rmtree(OUTPUT / "Common7", ignore_errors=True)
for f in ["Auxiliary", f"lib/{TARGET}/store", f"lib/{TARGET}/uwp"]:
  shutil.rmtree(OUTPUT / "VC/Tools/MSVC" / msvcv / f)
for f in OUTPUT.glob("*.msi"):
  f.unlink()
for f in ["Catalogs", "DesignTime", f"bin/{sdkv}/chpe", f"Lib/{sdkv}/ucrt_enclave"]:
  shutil.rmtree(OUTPUT / "Windows Kits/10" / f, ignore_errors=True)
for arch in ["x86", "x64", "arm", "arm64"]:
  if arch != TARGET:
    shutil.rmtree(OUTPUT / "VC/Tools/MSVC" / msvcv / f"bin/Host{arch}", ignore_errors=True)
    shutil.rmtree(OUTPUT / "Windows Kits/10/bin" / sdkv / arch)
    shutil.rmtree(OUTPUT / "Windows Kits/10/Lib" / sdkv / "ucrt" / arch)
    shutil.rmtree(OUTPUT / "Windows Kits/10/Lib" / sdkv / "um" / arch)


### setup.bat

SETUP_BAT = f"""@echo off

set ROOT=%~dp0

set MSVC_VERSION={msvcv}
set MSVC_HOST=Host{HOST}
set MSVC_ARCH={TARGET}
set SDK_VERSION={sdkv}
set SDK_ARCH={TARGET}

set MSVC_ROOT=%ROOT%VC\\Tools\\MSVC\\%MSVC_VERSION%
set SDK_INCLUDE=%ROOT%Windows Kits\\10\\Include\\%SDK_VERSION%
set SDK_LIBS=%ROOT%Windows Kits\\10\\Lib\\%SDK_VERSION%

set VCToolsInstallDir=%MSVC_ROOT%\\
set PATH=%MSVC_ROOT%\\bin\\%MSVC_HOST%\\%MSVC_ARCH%;%ROOT%Windows Kits\\10\\bin\\%SDK_VERSION%\\%SDK_ARCH%;%ROOT%Windows Kits\\10\\bin\\%SDK_VERSION%\\%SDK_ARCH%\\ucrt;%PATH%
set INCLUDE=%MSVC_ROOT%\\include;%SDK_INCLUDE%\\ucrt;%SDK_INCLUDE%\\shared;%SDK_INCLUDE%\\um;%SDK_INCLUDE%\\winrt;%SDK_INCLUDE%\\cppwinrt
set LIB=%MSVC_ROOT%\\lib\\%MSVC_ARCH%;%SDK_LIBS%\\ucrt\\%SDK_ARCH%;%SDK_LIBS%\\um\\%SDK_ARCH%
"""

(OUTPUT / "setup.bat").write_text(SETUP_BAT)

### setup.ps1

SETUP_PS1 = f"""$MSVC_VERSION = '{msvcv}'
$MSVC_HOST = 'Host{HOST}'
$MSVC_ARCH = '{TARGET}'
$SDK_VERSION = '{sdkv}'
$SDK_ARCH = '{TARGET}'

$MSVC_ROOT = "${{PSScriptRoot}}\\VC\\Tools\\MSVC\\${{MSVC_VERSION}}"
$SDK_INCLUDE = "${{PSScriptRoot}}\\Windows Kits\\10\\Include\\${{SDK_VERSION}}"
$SDK_LIBS = "${{PSScriptRoot}}\\Windows Kits\\10\\Lib\\${{SDK_VERSION}}"

$env:VCToolsInstallDir = "${{MSVC_ROOT}}\\"
$env:PATH = "${{MSVC_ROOT}}\\bin\\${{MSVC_HOST}}\\${{MSVC_ARCH}};${{PSScriptRoot}}\\Windows Kits\\10\\bin\\${{SDK_VERSION}}\\${{SDK_ARCH}};${{PSScriptRoot}}\\Windows Kits\\10\\bin\\${{SDK_VERSION}}\\${{SDK_ARCH}}\\ucrt;${{env:PATH}}"
$env:INCLUDE = "${{MSVC_ROOT}}\\include;${{SDK_INCLUDE}}\\ucrt;${{SDK_INCLUDE}}\\shared;${{SDK_INCLUDE}}\\um;${{SDK_INCLUDE}}\\winrt;${{SDK_INCLUDE}}\\cppwinrt"
$env:LIB = "${{MSVC_ROOT}}\\lib\\${{MSVC_ARCH}};${{SDK_LIBS}}\\ucrt\\${{SDK_ARCH}};${{SDK_LIBS}}\\um\\${{SDK_ARCH}}"
"""

(OUTPUT / "setup.ps1").write_text(SETUP_PS1)

print(f"Total downloaded: {total_download>>20} MB")
print("Done!")
```

</details>

After downloading, you can run `setup.ps1` in `msvc` directory to set environment variables on `process scope`. If you want to do this on `use scope` or `machine scope`, you need edit `ps` file.

``` ps
$MSVC_VERSION = '14.37.32822'
$MSVC_HOST = 'Hostx64'
$MSVC_ARCH = 'x64'
$SDK_VERSION = '10.0.22621.0'
$SDK_ARCH = 'x64'

$MSVC_ROOT = "${PSScriptRoot}\VC\Tools\MSVC\${MSVC_VERSION}"
$SDK_INCLUDE = "${PSScriptRoot}\Windows Kits\10\Include\${SDK_VERSION}"
$SDK_LIBS = "${PSScriptRoot}\Windows Kits\10\Lib\${SDK_VERSION}"

# Use System.Environment
# See https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_environment_variables?view=powershell-7.3
# See https://learn.microsoft.com/zh-tw/dotnet/api/system.environment.setenvironmentvariable?view=net-7.0


[Environment]::SetEnvironmentVariable('VCToolsInstallDir', "${MSVC_ROOT}\", 'User')
[Environment]::SetEnvironmentVariable('PATH', "${MSVC_ROOT}\bin\${MSVC_HOST}\${MSVC_ARCH};${PSScriptRoot}\Windows Kits\10\bin\${SDK_VERSION}\${SDK_ARCH};${PSScriptRoot}\Windows Kits\10\bin\${SDK_VERSION}\${SDK_ARCH}\ucrt;${env:PATH}", 'User')
[Environment]::SetEnvironmentVariable('INCLUDE', "${MSVC_ROOT}\include;${SDK_INCLUDE}\ucrt;${SDK_INCLUDE}\shared;${SDK_INCLUDE}\um;${SDK_INCLUDE}\winrt;${SDK_INCLUDE}\cppwinrt", 'User')
[Environment]::SetEnvironmentVariable('LIB', "${MSVC_ROOT}\lib\${MSVC_ARCH};${SDK_LIBS}\ucrt\${SDK_ARCH};${SDK_LIBS}\um\${SDK_ARCH}", 'User')
```

### Install IDEA

Download IDEA and install. After installing successfully we need to configure IDEA.

1. Install Plugins

    - [ ] Dracula Pro Theme
    - [ ] Atom Material Icons
    - [ ] Kafka
    - [ ] Big Data Tools Core
    - [ ] Jump to Line
    - [ ] Kubernetes
    - [ ] Redis Helper
    - [ ] Httpx Requests

2. Change Theme
3. Change Font

### Install DataGrip

Download DataGrip and install. Configure it like IDEA.

1. Change Theme
2. Change Font

## For Frontend Developer

### Download WebStorm

Download DataGrip and install. Configure it like IDEA.

1. Change Theme
2. Change Font
