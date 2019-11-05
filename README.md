# BlazorServerElectron

This Example is based on the [Electron.NET][1] project from **Gregor Biswanger** and **Robert Muehsig**

[1]: https://github.com/ElectronNET/Electron.NET


## Environment 

I discripte here the inital steps for setup this project for the .NetCore 3.0 and Electron.NET 5.30.1.

## Usage


### Setup Blazor-App
Start a new Blazor project. It should be a Blazor-Server-App. By default is "Configer for HTTPS" selceted. This should be uncheckt.

### Install Nuget

To activate and communicate with the "native" (sort of native...) Electron API include the ElectronNET.API NuGet package in your ASP.NET Core app.
```
PM> Install-Package ElectronNET.API
```

### Program.cs

You start Electron.NET up with an `UseElectron` WebHostBuilder-Extension.

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
     Host.CreateDefaultBuilder(args)
         .ConfigureWebHostDefaults(webBuilder =>
         {
             webBuilder.UseStartup<Startup>()
             .UseElectron(args);
         });
```

### Startup.cs

Open the Electron Window in the Startup.cs file:

```csharp
       // This method gets called by the runtime. Use this method to configure the HTTP request pipeline.
        public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
        {
            if (env.IsDevelopment())
            {
                app.UseDeveloperExceptionPage();
            }
            else
            {
                app.UseExceptionHandler("/Error");
            }

            app.UseStaticFiles();

            app.UseRouting();

            app.UseEndpoints(endpoints =>
            {
                endpoints.MapBlazorHub();
                endpoints.MapFallbackToPage("/_Host");
            });

            // Open the Electron-Window here
            Task.Run(async () => await Electron.WindowManager.CreateWindowAsync());
        }
```

### Start the Application

To start the application make sure you have installed the "[ElectronNET.CLI](https://www.nuget.org/packages/ElectronNET.CLI/)" packages as global tool:

```
dotnet tool install ElectronNET.CLI -g
```

At the first time, you need an Electron.NET project initialization. Type the following command in your ASP.NET Core folder:

```
electronize init
```

* Now a electronnet.manifest.json should appear in your ASP.NET Core project
* Now run the following:

```
electronize start
```
#### Note
> Only the first electronize start is slow. The next will go on faster.

### Debug

Start your Electron.NET application with the Electron.NET CLI command. In Visual Studio attach to your running application instance. Go in the __Debug__ Menu and click on __Attach to Process...__. Sort by your projectname on the right and select it on the list.

### Usage of the Electron-API

A complete documentation will follow. Until then take a look in the source code of the sample application:  
[Electron.NET API Demos](https://github.com/ElectronNET/electron.net-api-demos)  

In this YouTube video, we show you how you can create a new project, use the Electron.NET API, debug a application and build an executable desktop app for Windows: [Electron.NET - Getting Started](https://www.youtube.com/watch?v=nuM6AojRFHk)  
  
### Build

Here you need the Electron.NET CLI as well. Type the following command in your ASP.NET Core folder:

```
electronize build /target win
```

There are additional platforms available:

```
electronize build /target win
electronize build /target osx
electronize build /target linux
```

Those three "default" targets will produce x64 packages for those platforms.

For certain NuGet packages or certain scenarios you may want to build a pure x86 application. To support those things you can define the desired [.NET Core runtime](https://docs.microsoft.com/en-us/dotnet/core/rid-catalog), the [electron platform](https://github.com/electron-userland/electron-packager/blob/master/docs/api.md#platform) and [electron architecture](https://github.com/electron-userland/electron-packager/blob/master/docs/api.md#arch) like this:

```
electronize build /target custom win7-x86;win32 /electron-arch ia32 
```

The end result should be an electron app under your __/bin/desktop__ folder.
