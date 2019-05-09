---
layout: single
title:  "Creating a windows service to monitor a certain folder for changes"
excerpt: "Windows Services"
date:   2019-05-09 11:41:00 +0200
tags:
  - Windows Service 
  - Visual Studio
---

First, in the `Mynewservice.cs` file:

- Services report their status to the `Service Control Manager` so that a user can tell whether a service is functioning correctly, basic services report the `Running` and the `Stopped` status, you can use other flags to report `Pending` status upon start or stop if your `OnStart()` or `OnStop()` methods are long-running methods:

```
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

```