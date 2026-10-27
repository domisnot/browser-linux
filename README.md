# browser-linux

GitHub Codespaces Linux Desktop Environment (XFCE)

This repository provides a fully functional XFCE Linux desktop environment accessible directly from your browser via noVNC inside GitHub Codespaces.

1. Initial DevContainer Setup

To ensure GitHub Codespaces detects your container configuration, your devcontainer.json file must be placed inside a .devcontainer folder at the root of your repository.

Create the Directory & Move the File

Run these commands in your VS Code terminal:
```
mkdir -p .devcontainer
mv devcontainer.json .devcontainer/devcontainer.json
```

Complete devcontainer.json Configuration

Ensure your .devcontainer/devcontainer.json file contains the following configuration:
```
{
  "name": "Linux Desktop",
  "image": "mcr.microsoft.com/devcontainers/base:ubuntu",
  "features": {
    "ghcr.io/devcontainers/features/desktop-lite:1": {
      "desktopUI": "xfce"
    }
  },
  "forwardPorts": [6080],
  "portsAttributes": {
    "6080": {
      "label": "Desktop Lite (noVNC)"
    }
  },
  "postCreateCommand": "sudo apt update && sudo apt install -y xfce4 xfce4-goodies git-lfs"
}
```

Apply Configuration

Press Ctrl + Shift + P (or Cmd + Shift + P on Mac) to open the Command Palette.

Search for Codespaces: Rebuild Container and press Enter.

2. Accessing the Linux Desktop

Once the container finishes building, open the Ports tab in VS Code (bottom panel).

Locate Port 6080 (Desktop Lite).

Click the Globe icon (Open in Browser) to open your Linux desktop tab.

3. Fixing White Screen or Missing Desktop Panels

If the noVNC page opens to a blank white screen, missing background, or empty taskbar:

Step 1: Install XFCE Desktop Components
```
sudo apt update
sudo apt install -y xfce4 xfce4-goodies
```

Step 2: Force Start Desktop Components

Run these commands to force the background, taskbar, and window managers to render on display :1:
```
DISPLAY=:1 xfce4-panel &
DISPLAY=:1 xfdesktop &
DISPLAY=:1 xfwm4 &
```

Step 3: Adjust Screen Sizing

If taskbars or menus are cut off at the bottom or sides of your screen:

Click the Gear (Settings) icon on the dark grey panel on the far left edge of the noVNC tab.

Under Scaling Mode, switch to Remote Resizing or Local Scaling.

Refresh the page (Ctrl + R).

4. Browser Setup (Bypassing Ubuntu Snap Errors)

Standard apt browser installs (like Firefox or Chromium) fail inside Docker containers because Ubuntu uses Snap packages by default, which require snapd.

Install Google Chrome Native Package (.deb)
```
wget https://dl.google.com/linux/direct/google-chrome-stable_current_amd64.deb
sudo apt update
sudo apt install -y ./google-chrome-stable_current_amd64.deb
```

Launch Google Chrome

Chromium-based browsers require the --no-sandbox flag to open inside Docker/Codespaces:
```
DISPLAY=:1 google-chrome-stable --no-sandbox &
```

Fix the Applications Menu Shortcut

To allow Chrome to launch directly when clicking Applications Menu -> Web Browser:
```
sudo sed -i 's|Exec=/usr/bin/google-chrome-stable %U|Exec=/usr/bin/google-chrome-stable --no-sandbox %U|g' /usr/share/applications/google-chrome.desktop
sudo update-alternatives --set x-www-browser /usr/bin/google-chrome-stable
```

5. Troubleshooting Git Push & LFS Errors

If running git push fails with the error 'git-lfs' was not found on your path:

Option A: Install Git LFS (Recommended)
```
sudo apt update && sudo apt install -y git-lfs
git lfs install
git push origin main
```

Option B: Remove the Local Git LFS Hook
```
rm -f .git/hooks/pre-push
git push origin main
```
