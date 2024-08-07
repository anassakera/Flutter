## How to change the default Flutter application icon :

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
