# WinSpotlightKDE
A utility for automatically downloading Windows Spotlight images, and setting them as your desktop, lock screen, and display manager background in KDE.

This project is specifically for Linux systems using systemd, running KDE and using SDDM as the display manager, such as Kubuntu and Manjaro KDE. However, with a little work, it could easily be modified to work with other desktop environments or display managers, or with an alternative to systemd timers such as cron.
<br/><br/><br/>

Thank you to [ORelio](https://github.com/ORelio/Spotlight-Downloader) on GitHub for their documentation on how the Spotlight API works. Without it, this project would have been significantly harder to finish.

## Dependencies
- Bash (may work with other shells, but this has not been tested)
- [cURL](https://curl.haxx.se/) (for sending an http request to the Spotlight API)
- [Jq](https://stedolan.github.io/jq/) (for parsing JSON returned by the API)

## Installation
1. Clone or download this repo.
2. Open a terminal and `cd` into the repo folder that you downloaded.
3. Make the `winspotlightkde` script executable:
    ```
    chmod o+w winspotlightkde
    ```
4. Copy the `winspotlightkde` script to the `opt` folder on your system:
    ```
    sudo cp winspotlightkde /opt/
    ```
5. Copy the WinSpotlightKDE `.service` and `.timer` files to the systemd user unit directory:
    ```
    sudo cp winspotlightkde.service winspotlightkde.timer /usr/lib/systemd/user/
    ```
6. Reload the systemd user daemon:
    ```
    systemctl --user daemon-reload
    ```
7. Enable and start the WinSpotlightKDE systemd timer
    ```
    systemctl --user enable winspotlightkde.timer
    systemctl --user start winspotlightkde.timer
    ```

### SDDM
If you're using SDDM, and you want WinSpotlightKDE to change your SDDM background as well, there are a few additional steps:

1. Determine what SDDM theme you're currently using. This can be accomplished by going into KDE settings -> Workspace -> Startup and Shutdown -> Login Screen (SDDM) and seeing what theme is highlighted.
2. Open up a terminal, and `cd` to `/usr/share/sddm/themes/`
3. Create a copy of the theme you're currently using, called "winspotlightkde":
    ```
    sudo cp -r your-theme/ winspotlightkde
    ```
4. Change the permissions of the newly-created theme folder, to allow non-root users to add new files, and replace new files that are added after this point:
    ```
    sudo chmod o+w winspotlightkde
    ```
5. `cd` into the `winspotlightkde` folder, and open up `metadata.desktop` in your favourite text editor:
    ```
    sudo nano metadata.desktop
    ```
6. Change the line that starts with `name=` to `name=WinSpotlightKDE`. You can repeat this step on the other lines as well, but this isn't necessary.

7. Open up `theme.conf.user` in your favourite text editor, and change the line that starts with `background=` to `background=sddm.jpeg`.

8. Go back to SDDM settings (the part of settings you were just in to see what theme you were using), and change your theme to the "WinSpotlightKDE" theme.

## Configuration
The following options can be changed by modifying the `winspotlightkde` script, changing the various variables at the top of the file:

### **hRes**
The horizontal resolution of the image that will be downloaded. Normally, this should be set to the horizontal resolution of your monitor.

### **vRes**
The vertical resolution of the image that will be downloaded. Same deal as above.

### **locale**
An option sent to the Spotlight API. Since the Spotlight API isn't at all documented, I'm not sure exactly what this does, but presumably it affects what kind of images the API sends to you.

### **country**
Same as above.

### **imgPath**
The path where images downloaded by the utility will be stored.

### **deleteImagesAfterNumDays**
When the utility runs, all images as old or older than the number of days specified here will be deleted from the directory specified previously. If the value specified is less than or equal to 0, images will be preserved indefinitely.

### **setDesktop**
If set to yes, the desktop background will be changed when the utility downloads a new image.

### **setLockscreen**
Same as above, but for the lock screen.

### **setDM**
Same as above, but for the display manager (log-in screen).
