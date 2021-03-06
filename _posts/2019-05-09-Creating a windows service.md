---
layout: single
title:  "Creating a windows service to monitor a certain folder for changes"
excerpt: "Windows Services"
date:   2019-05-09 11:41:00 +0200
tags:
  - Windows Service 
  - Visual Studio
---

## Writing the service:

First, in the `Mynewservice.cs` file:

- Services report their status to the `Service Control Manager` so that a user can tell whether a service is functioning correctly, basic services only report the `Running` and the `Stopped` status, you can use other flags to report `Pending` status upon start or stop if your `OnStart()` or `OnStop()` methods are long-running methods.

{% highlight javascript linenos %}
[DllImport("advapi32.dll", SetLastError = true)]
private static extern bool SetServiceStatus(System.IntPtr handle, ref ServiceStatus serviceStatus);

public enum ServiceState
{
    SERVICE_STOPPED = 0x00000001,
    SERVICE_START_PENDING = 0x00000002,
    SERVICE_STOP_PENDING = 0x00000003,
    SERVICE_RUNNING = 0x00000004,
    SERVICE_CONTINUE_PENDING = 0x00000005,
    SERVICE_PAUSE_PENDING = 0x00000006,
    SERVICE_PAUSED = 0x00000007,
}

[StructLayout(LayoutKind.Sequential)]
public struct ServiceStatus
{
    public int dwServiceType;
    public ServiceState dwCurrentState;
    public int dwControlsAccepted;
    public int dwWin32ExitCode;
    public int dwServiceSpecificExitCode;
    public int dwCheckPoint;
    public int dwWaitHint;
};
{% endhighlight %}

So the `OnStart()` method will look like this:

{% highlight javascript linenos %}
protected override void OnStart(string[] args)
{
    eventLog1.WriteEntry("Service is on start!");
    
    // Update the service state to Start Pending.
    
    ServiceStatus serviceStatus = new ServiceStatus();
    serviceStatus.dwCurrentState = ServiceState.SERVICE_START_PENDING;
    serviceStatus.dwWaitHint = 100000; //SCM waiting time for the service to start.
    SetServiceStatus(this.ServiceHandle, ref serviceStatus);

    using (StreamWriter fs = File.CreateText(@"D:\ServiceTests\log.txt"))
    {
        string[] tempFiles = Directory.GetFiles(@"D:\ServiceTests");
        fs.WriteLine("::Beginning of log::\n");
        fs.WriteLine($"There is a total of {tempFiles.Length} files in this directory.\n");
    }

    eventLog1.WriteEntry("Log created", EventLogEntryType.Information, eventId++);

    Timer timer = new Timer();
    timer.Interval = 5000; // 5 seconds
    timer.Elapsed += new ElapsedEventHandler(this.OnTimer);
    timer.Start();

    // Update the service state to Running.
    serviceStatus.dwCurrentState = ServiceState.SERVICE_RUNNING;
    SetServiceStatus(this.ServiceHandle, ref serviceStatus);
}
{% endhighlight %}

The `OnTimer()` event will look like this:

{% highlight javascript linenos %}
private void OnTimer(object sender, ElapsedEventArgs e)
{
    files = Directory.GetFiles(@"D:\ServiceTests");

    if (files_current == null)
    {
        files_current = files;
    }
    else
    {
        if (files.Except(files_current).Count() != 0)
        {
            diff = files.Except(files_current).ToArray();

            foreach (var VARIABLE in diff)
            {
                File.AppendAllText(@"D:\ServiceTests\log.txt", $"New file added: \"{VARIABLE}\"\n");
            }

            files_current = files.Union(files_current).ToArray();

            File.AppendAllText(@"D:\ServiceTests\log.txt", $"There is a total of {files_current.Length} files in this folder\n");
        }
        else if (files_current.Except(files).Count() != 0)
        {
            diff = files_current.Except(files).ToArray();

            foreach (var VARIABLE in diff)
            {
                File.AppendAllText(@"D:\ServiceTests\log.txt", $"File deleted or changed: \"{VARIABLE}\"\n");
            }

            files_current = files;

            File.AppendAllText(@"D:\ServiceTests\log.txt", $"There is a total of {files_current.Length} files in this folder\n");
        }
    }
}
{% endhighlight %}

Finally the `OnStop()` method will look like this:

{% highlight javascript linenos %}
protected override void OnStop()
{
    eventLog1.WriteEntry("Service is on stop!");

    ServiceStatus serviceStatus = new ServiceStatus();
    serviceStatus.dwCurrentState = ServiceState.SERVICE_STOP_PENDING;
    serviceStatus.dwWaitHint = 100000; //SCM waiting time for the service to stop.
    SetServiceStatus(this.ServiceHandle, ref serviceStatus);

    serviceStatus.dwCurrentState = ServiceState.SERVICE_STOPPED;
    SetServiceStatus(this.ServiceHandle, ref serviceStatus);
}
{% endhighlight %}

## Debuggin the service:

Go to the `Program.cs` class and paste this code as your main method:

{% highlight javascript linenos %}
static void Main(string[] args)
{
    if (Environment.UserInteractive)
    {
        hmzservice service1 = new hmzservice(args);
        service1.TestStartupAndStop(args);
    }
    else
    {
        //Here goes the main method for your service        
        ServiceBase[] ServicesToRun;
        ServicesToRun = new ServiceBase[]
        {
            new hmzservice(args)
        };
        ServiceBase.Run(ServicesToRun);
    }
}
{% endhighlight %}

Then in your service class add this method:

{% highlight javascript linenos %}
internal void TestStartupAndStop(string[] args)  
    {  
        this.OnStart(args);  
        Console.ReadLine();  
        this.OnStop();  
    } 
{% endhighlight %}

- This way the service can be run as a console application and can be installed as a windows service without reverting the changes that was made in the `Program.cs` class, just by switching between a `Console Application` and a `Windows Application` in the `Project Properties` > `Output`.

## Installing the service:

Open `Developer Command Prompt for VS`, navigate with `cd` command to the folder containing your service (Usually the debug folder inside the project folder) and type:

{% highlight javascript linenos %}
//To install the service
installutil MyService.exe

//To delete the service
installutil /u MyService.exe
{% endhighlight %}

- Make sure to add installers to the service before building your project in order to be installed successfully by the `installutil.exe` utility and for logging setup the service should run under the `LocalSystem` account or it won't work and finally mark the `StartupType` as `Automatic` in order for the service to start automatically.

### For more information on how to setup logging see the Microsoft documentation of Windows Services: [Windows Services Docs](https://docs.microsoft.com/en-us/dotnet/framework/windows-services/walkthrough-creating-a-windows-service-application-in-the-component-designer)