# winget-manual-installation

**This guide assumes you don't have MS Store and don't want to have it. If this is not your way then use it to install winget. On LTSC you can get it if you run `wsreset -i` in PowerShell (maybe you'll need to run it twice).**

**DISCLAIMER: You could use `Add-AppxPackage` instead of `Add-AppxProvisionedPackage` for dependencies but you can't for winget itself because package will be not licensed and won't work. But I still recommend you to use `Add-AppxProvisionedPackage` everywhere. Also I renaming and moving files here to make commands work for everyone, you could use your own paths and relative too, it'll work.**

First we need to install dependencies. We have to install 2 packages to make everything work.
 - Microsoft.UI.Xaml
 
 (For 1.4.10173 we need Microsoft.UI.Xaml.2.7 but in future version may change.)
 
 To get it go to https://www.nuget.org/packages/Microsoft.UI.Xaml/#versions-body-tab and click on latest compatible version. I.e. we need 2.**7** so latest compatible is 2.**7**.3 at the moment (and also 2.**7**.4 preview but you never want to install a preview, trust me). 2.**8**.X won't be compatible since they're Microsoft.UI.Xaml.2.**8**.
  
  Now click on "Download package" on right side of page. Now we got a weird `.nupkg` file, what we should do with it? Change the extension to `.zip` (you should have extensions enabled in Explorer) then open it as usual archive. You'll see some folders and files inside. We need only one which is in `tools/AppX/<your_architecture>/Release` folder (for x64 it'll be `tools/AppX/x64/Release`. Just copy that file in root of C volume and rename it to `xaml.appx`. We'll get back to it later.
  - Microsoft.VCLibs.140.00.UWPDesktop
 
 This one is easier to get. Go to https://learn.microsoft.com/en-us/troubleshoot/developer/visualstudio/cpp/libraries/c-runtime-packages-desktop-bridge and download one for your architecture (for x64 it'll be `Microsoft.VCLibs.x64.14.00.Desktop.appx`). Rename it to `vcl.appx` and move to root of C volume.

Now time to get Microsoft.DesktopAppInstaller itself. Go to https://github.com/microsoft/winget-cli/releases/latest and download file with `.msixbundle` extension and file with `.xml` extension. Rename first to `dai.msixbundle` and second to `lic.xml` and move them to root of C volume too.

Now let's install all of this. Open PowerShell as administrator and run this 3 commands (no, good old cmd won't do):
```ps
Add-AppxProvisionedPackage -Online -PackagePath "C:\xaml.appx" -SkipLicense
Add-AppxProvisionedPackage -Online -PackagePath "C:\vcl.appx" -SkipLicense
Add-AppxProvisionedPackage -Online -PackagePath "C:\dai.msixbundle" -LicensePath "C:\lic.xml"
```

Now open PowerShell as normal user and type `winget`. If no error occurs then evetyrhing went well. If there is an error then something messed up (probably with dependencies). Problem is that Add-AppxProvisionedPackage doesn't show you anything if something went wrong (-Verbose won't help too). In this case try running `Add-AppxPackage -Path "C:\dai.msixbundle"`, unlike Add-AppxProvisionedPackage it'll show you what dependency missing and you can install it and then run `Add-AppxProvisionedPackage -Online -PackagePath "C:\dai.msixbundle" -LicensePath "C:\lic.xml"` again. At least I hope this will help.
