# Hackintosh Bluetooth Wakeup Fix (via Sleepwatcher)

An automated guide to fix the notorious Hackintosh bug where Bluetooth stops working after waking up from sleep. 

On many Hackintosh builds, the Bluetooth module (connected via an internal USB header) fails to reinitialize properly after power state changes due to USB power management or controller sleep issues. This setup automatically restarts the macOS `bluetoothd` daemon upon every wakeup, forcing macOS to re-probe the USB ports and bring your Bluetooth back to life completely hands-free.

---

## Prerequisites (Install Homebrew)

To set this up, we need **Homebrew** to install the sleep monitoring utility.

### 1. Install Homebrew
Open your Terminal and paste the following official command:

```bash
/bin/bash -c "$(curl -fsSL '[https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh](https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)')"

```

### 2. Verify Installation

Restart your Terminal and verify that Homebrew is working properly on your system:

```bash
brew --version

```

---

## Step-by-Step Configuration

### 1. Install and Start Sleepwatcher

Sleepwatcher is a command-line tool that monitors macOS sleep and wakeup events.

```bash
brew install sleepwatcher
brew services start sleepwatcher

```

### 2. Configure Sudoers (Passwordless Execution)

Restarting `bluetoothd` requires root privileges (`sudo`). To let the script run seamlessly in the background without prompting you for a password every time you open your PC, we must add an exception to the `sudoers` file.

1. Open the sudoers file using the Nano editor:
```bash
sudo EDITOR=nano visudo
```

2. Scroll to the very bottom of the file and append the following line:
   ```text
   %admin ALL=(ALL) NOPASSWD: /usr/bin/pkill bluetoothd
```
   
3. Save and exit: Press `Ctrl + O`, then `Enter`, then `Ctrl + X`.

### 3. Create the Wakeup Script

Sleepwatcher automatically looks for a hidden script named `.wakeup` in your home directory upon system wakeup.

1. Create and open the file:
```bash
nano ~/.wakeup
```

```
2. Paste the following bash script:
   ```bash
   #!/bin/bash
   # Forces macOS to restart the bluetooth daemon on Hackintosh wakeup
   sudo /usr/bin/pkill bluetoothd
   

```

3. Save and exit: Press `Ctrl + O`, then `Enter`, then `Ctrl + X`.

### 4. Make the Script Executable

Give the system permission to execute your newly created script:

```bash
chmod +x ~/.wakeup

```

---

## Testing the Setup

### Manual Test

Before putting your Hackintosh to sleep, verify that the script runs without asking for a password:

```bash
~/.wakeup

```

Your Bluetooth icon in the menu bar should toggle/flicker for a split second. If it didn't ask for a password, your `visudo` configuration is correct.

### Automated Test

1. Put your Hackintosh to sleep (Apple Menu -> Sleep).
2. Wait 10–15 seconds until the PC fully powers down.
3. Wake it up. Your Bluetooth should be functional within 1–2 seconds.

## Troubleshooting

If the automation stops working after a major macOS update, simply restart the sleepwatcher background service:

```bash
brew services restart sleepwatcher


```
