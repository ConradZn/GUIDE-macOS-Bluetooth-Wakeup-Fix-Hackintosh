## Prerequisites

You need **Homebrew** installed. If you don't have it, open your Terminal and run:

```bash
/bin/bash -c "$(curl -fsSL '[https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh](https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)')"

```
---

## Step-by-Step Installation

### 1. Install and Start Sleepwatcher

Sleepwatcher is a command-line tool that monitors macOS sleep/wakeup events.

```bash
brew install sleepwatcher
brew services start sleepwatcher

```

### 2. Configure Sudoers (Passwordless execution)

Since restarting `bluetoothd` requires root privileges (`sudo`), we must allow your user (or the admin group) to run this specific command without asking for a password.

1. Open the sudoers configuration file:
```bash
sudo EDITOR=nano visudo

```
2. Scroll to the very bottom of the file and append the following line:

```text
%admin ALL=(ALL) NOPASSWD: /usr/bin/pkill bluetoothd
```

```
3. Save and exit: Press `Ctrl + O`, then `Enter`, then `Ctrl + X`.

### 3. Create the Wakeup Script
Sleepwatcher automatically looks for a hidden file named `.wakeup` in your home directory when the Mac wakes up.

1. Create and open the file:
   ```bash
   nano ~/.wakeup
   

```

2. Paste the following bash script:
```bash
#!/bin/bash
# Automatically restart the bluetooth daemon on wakeup
sudo /usr/bin/pkill bluetoothd


```



```
3. Save and exit: Press `Ctrl + O`, then `Enter`, then `Ctrl + X`.

### 4. Make the Script Executable
Give the system permission to execute your newly created script:

```bash
chmod +x ~/.wakeup

```

---

## Testing

### Manual Test

Before putting your Mac to sleep, verify that the script runs without asking for a password:

```bash
~/.wakeup

```

Your Bluetooth icon in the menu bar should flicker/restart for a split second. If it doesn't ask for a password, it works!

### Automated Test

1. Put your Mac to sleep (Apple Menu -> Sleep or close the lid).
2. Wait 10–15 seconds.
3. Wake your Mac up. Your Bluetooth should be functional immediately.

## Troubleshooting

If it stops working after a macOS update, ensure the `sleepwatcher` service is still running:

```bash
brew services restart sleepwatcher

```
