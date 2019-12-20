## Set Mac Like Theme for Ubuntu 18.04

This is the record of the process of how to customize ubuntu desktop theme. I use mac like theme.

The first thing is to install gnome TweakTool:

``` shell
sudo apt-get update
sudo apt-get install gnome-tweak-tool
```

![tweak tool](https://raw.githubusercontent.com/sldai/Blog/master/Ubuntu%20Mac%20Theme%20Process/tweak-tool.png)

Then install shell extension:

``` shell
sudo apt-get install gnome-shell-extensions
```

Then add extension for genome. Open the website for gnome extensions (https://extensions.gnome.org/).
![extensionsite](https://raw.githubusercontent.com/sldai/Blog/master/Ubuntu%20Mac%20Theme%20Process/gnome_extension.png)

To add extensions, choose a extension and click on. You may need chrome extension recommended in this page installed.
![clickon](https://raw.githubusercontent.com/sldai/Blog/master/Ubuntu%20Mac%20Theme%20Process/click_on.png)

I recommend add following extensions:

User Theme (must): allow user define theme in ~/.themes and ~/.icons folders.
![floder](https://raw.githubusercontent.com/sldai/Blog/master/Ubuntu%20Mac%20Theme%20Process/folder.png)

Dash to Dock: Customize you Dock.

![dock](https://raw.githubusercontent.com/sldai/Blog/master/Ubuntu%20Mac%20Theme%20Process/dock.png)

Removable Drive Menu: usb indicator.

open weather.

-----------

### Set GTK Theme

GTK theme includes themes of your file manager and other system applications. Download mac theme zip from [McSierra Compact](https://www.gnome-look.org/p/1013741/), then extract it into ~/.themes folder. Then choose the application theme in tweak tool.

![themefolder](https://raw.githubusercontent.com/sldai/Blog/master/Ubuntu%20Mac%20Theme%20Process/set_app_theme.png)

-----------

### Set Gnome Shell Theme

It will affect your top bar. Install [human](https://www.gnome-look.org/p/1171095/), then extract it into into ~/.themes folder. Then choose the shell theme in tweak tool.

-----------

### Set File Icons

Download [Cupertino-Catalina](https://www.gnome-look.org/p/1102582/), then extract it into into ~/.icons folder. Then choose the icon theme in tweak tool.

-----------

### Set Grub Theme

It affect the boot graphical interface. Download [Grub-theme-vimix](https://www.gnome-look.org/p/1009236/). Open terminal in the extracted folder,

``` shell
sudo .\install
```

![grub](https://raw.githubusercontent.com/sldai/Blog/master/Ubuntu%20Mac%20Theme%20Process/gubtheme.jpg)

--------

### Set Lockscreen Theme

I use blurred wall paper for lockscreen. Download [High ubunterra](https://www.gnome-look.org/p/1207015/) with right version corresponding to your OS. Open terminal in the extracted folder. 

``` shell
chmod +x ./install.sh
./install.sh
```

Now you just choose your image right click mouse button you will see script and "SetAsWallpaper" just click on it, wallpaper already set.

To uninstall use command ./uninstall.sh.

![lockscreen](https://cdn.pling.com/img/d/7/9/2/024c75931e497b8071f8567a9e71822f3889.png)