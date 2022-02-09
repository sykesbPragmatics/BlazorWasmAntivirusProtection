﻿# Blazor Wasm Antivirus Protection

[![Nuget (with prereleases)](https://img.shields.io/nuget/v/BlazorWasmAntivirusProtection.svg?logo=nuget)](https://www.nuget.org/packages/BlazorWasmAntivirusProtection)  [![Donate](https://img.shields.io/badge/Donate-PayPal-green.svg)](https://www.paypal.com/cgi-bin/webscr?cmd=_donations&business=7CRGWPYB5AKJQ&currency_code=EUR&source=url)

This package attempts to guard against false positives from antiviruses that flag Blazor Wasm as malware,  [until (or if) Microsoft gives us an official solution](https://github.com/dotnet/aspnetcore/issues/36978).


## Confirmed success against:
1. BitDefender Total Security (v26.0.10.45)
2. BitDefender Endpoint Security Tool (v7.4.3.146)
3. Smoothwall Firewall - [Confirmed by](https://github.com/MudBlazor/MudBlazor/issues/3883#issuecomment-1031357095) [peterthorpe81](https://github.com/peterthorpe81)

> 🔔 *If you have used this package and has helped you bypass any false positives from other security software, please consider creating an issue with your experience to contribute to this list.*

## What does this package do ?
This package injects some custom MSBuild tasks that do the following:
1. Changes the MZ header of all client assemblies to BZ, a custom header, so that firewalls and antiviruses don't see them as executables. (more info [here](https://en.wikipedia.org/wiki/DOS_MZ_executable))
2. Renames the extension of all client assemblies from .dll to .bin
3. Adds a lib.module.js that contains a `beforeStart` blazor initialization method (more info [here](https://docs.microsoft.com/en-us/aspnet/core/blazor/javascript-interoperability/?view=aspnetcore-6.0#javascript-initializers)), that uses a custom `loadBootResource` function to restore the MZ header of the assemblies after downloaded but before loaded by dotnet.wasm

## How to use
1. Add the nuget package in your **Client** (wasm) **AND** your **Server** (if using blazor wasm hosted) projects
```
dotnet add package BlazorWasmAntivirusProtection
``` 
2. (Progressive Web Application Only): If you are using the Blazor Wasm PWA template, update the following line in your `service-worker.published.js` file to include `.bin` files:

```js
const offlineAssetsInclude = [/\.bin$/, /\.dll$/, /\.pdb$/, /\.wasm/, /\.html/, /\.js$/, /\.json$/, /\.css$/, /\.woff$/, /\.png$/, /\.jpe?g$/, /\.gif$/, /\.ico$/, /\.blat$/, /\.dat$/ ];
```

3. Publish your app in Release mode and test it!
```
dotnet publish Server\BlazorHostedSampleApp.Server.csproj -c Release
```
*Nuget package page can be found [here](https://www.nuget.org/packages/BlazorWasmAntivirusProtection).*

## Configuration
The following options allow you to customize the tasks executed by this package.
### **Custom dll rename extension**
If you want to use a different extension for renaming dlls, for example ".blz", add the following property in the **published** project's .csproj file (**Server** project if using blazor hosted).
```xml
<RenameDllsTo>blz</RenameDllsTo>
```

### **Disable dll rename**
You can disable dll renaming by adding the following property in the **published** project's .csproj file (**Server** project if using blazor hosted).
```xml
<DisableRenamingDlls>true</DisableRenamingDlls>
```

### **Disable dll header changing**
You can disable dll header changing by adding the following property in your **Client** project's .csproj file.
```xml
<DisableChangingDllHeaders>true</DisableChangingDllHeaders>
```

## Samples / Demo
You can find a sample app using this package [here](https://blazor-antivirus-block.azurewebsites.net/).


## Special Thanks
This work was inspired by the post in https://github.com/dotnet/aspnetcore/issues/31048#issuecomment-915152791  by github user [tedd](https://github.com/tedd)

## Release Notes

<details open="open"><summary>1.3</summary>
    
>- Added support for Blazor Wasm PWA apps
</details>


<details><summary>1.2</summary>
    
>- Fixed sequential publishing issue.
</details>

<details><summary>1.0</summary>
    
>- Added customization options.
</details>

<details><summary>0.1</summary>
    
>- Initial release.
</details>
