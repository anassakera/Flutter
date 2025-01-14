# How to change the default Flutter application icon :

1 -  you should have "assets\images\logo.png"

2 - paste this command in the VSCode Terminal "flutter pub add flutter_launcher_icons"

3 - paste the following script in "pupspect.yaml" and change "image_path" :

```yaml
dev_dependencies:
  flutter_test:
    sdk: flutter

flutter_launcher_icons:
  android: "launcher_icon"
  ios: true
  image_path: "assets/images/logo.png"
```
4 - paste the following commands  in the VSCode Terminal : 
```PowerShell 
flutter pub get
flutter pub run flutter_launcher_icons
```

# How to Exporting Tables from phpMyAdmin

## Step 1: Access phpMyAdmin in cPanel

1. Log in to your cPanel account.
2. Scroll down to the **"Databases"** section and click on **"phpMyAdmin"**.

## Step 2: Select the Database

1. In phpMyAdmin, you'll see a list of databases on the left sidebar. Click on the database that contains the tables you want to export.

## Step 3: Export the Tables

1. After selecting the database, you'll see a list of tables in that database.
2. Click on the **"Export"** tab at the top of the page.
3. Choose the **"Custom"** export method to select specific tables.
4. In the **"Tables"** section, select the tables you want to export.
5. Under **"Output"**, you can select whether you want the output as a file (default) and specify the file format (e.g., SQL).
6. If you only want the table structure (without data), make sure to set **"Data"** to **"Structure only"**.
7. Once you've made your selections, scroll down and click **"Go"**.

## Step 4: Download the Exported File

The export will start, and depending on your settings, it will either download directly or display the SQL code that you can copy.

# How to Restore Default Scale (Window Size) in VSCode

To restore the default scale or size of the window in Visual Studio Code, you can reset the zoom level to its default value. Here's how you can do it:

## Option 1: Using Keyboard Shortcuts
- **Reset Zoom**: Press `Ctrl + 0` (or `Cmd + 0` on macOS) to reset the zoom level to the default (100%).

## Option 2: Using the Command Palette
1. Open the Command Palette by pressing `Ctrl + Shift + P` (or `Cmd + Shift + P` on macOS).
2. Type `View: Reset Zoom` and select it. This will restore the zoom level to the default.

## Option 3: Editing Settings Directly
1. Open the settings by pressing `Ctrl + ,` (or `Cmd + ,` on macOS).
2. Search for "zoom" in the search bar.
3. Set the "Window: Zoom Level" to `0` to restore the default size.

This will restore Visual Studio Code to its default scaling and size.


# How to connect your Device with scrcpy :
```powershell
adb devices
adb -s 112111541Q011813 tcpip 5555
adb connect 192.168.1.18:5555
scrcpy -s 192.168.1.18:5555
```


