title: Windows Device Driver
date: 2015-11-22 11:26:52
categories: Techniques
---

**This guide is for the target of pci bus device and its sub-devices resources allocation.**


## For Bus Driver

First you should design a customized data structure for storing the resource you gethered, let's just called it DEVICE_EXTENSION:

``` c
typedef structure {

  WDFDEVICE device;

  ULONG port0Base;
  ULONG port0Length;

  ULONG port1Base;
  ULONG port1Length;

  ....

} DEVICE_EXTENSION, *PDEVICE_EXTENSION;
```

suppose your bus device has two io ports.

then you should register a callback function **before** calling WdfDeviceCreate:

``` c
EVT_WDF_DEVICE_RESOURCES_PREPARE_HARDWARE Bus_EvtDevicePrepareHardware;

#ifdef ALLOC_PRAGMA
pragma alloc_text (PAGE, Bus_EvtDevicePrepareHardware);
...
#endif

NTSTATUS xxxEvtDeviceAdd(IN WDFDRIVER Driver, IN PWDFDEVICE_INIT DeviceInit)
{
    ...

    WDF_PNPPOWER_EVENT_CALLBACKS pnpPowerCallbacks;

    WDF_PNPPOWER_EVENT_CALLBACKS_INIT(&pnpPowerCallbacks);

    pnpPowerCallbacks.EvtDevicePrepareHardware = Bus_EvtDevicePrepareHardware;

    WdfDeviceInitSetPnpPowerEventCallbacks(DeviceInit, &pnpPowerCallbacks);
    ....
    // WdfDeviceCreate();
}

NTSTATUS Bus_EvtDevicePrepareHardware(IN WDFDEVICE Device, IN WDFCMRESLIST ResourcesRaw, IN WDFCMRESLIST ResourcesTranslated)
{
    // use WdfCmResourceListGetCount(ResourcesRaw) to get the number of resources.
    // use WdfCmResourceListGetDescriptor(ResourcesRaw, i) to get the i-th PCM_PARTIAL_RESOURCE_DESCRIPTOR.
    // use descriptor->Type to determine the resource type (CmResourceTypePort or CmResourceTypeInterrupt or something else).
    // save the gethered resources to the DEVICE_EXTENSION structure
    // that you previously defined.
}
```

When enumerating the subdevices, the bus device's ResourcesQuery callback and ResourceRequirementsQuery callback will be called.
The callback registration should also before the invocation of WdfDeviceCreate of the PDO.

``` c
EVT_WDF_DEVICE_RESOURCES_QUERY Bus_EvtDeviceResourcesQuery;
EVT_WDF_DEVICE_RESOURCE_REQUIREMENTS_QUERY Bus_EvtDeviceResourceRequirementsQuery;

#ifdef ALLOC_PRAGMA
pragma alloc_text (PAGE, Bus_EvtDeviceResourcesQuery);
pragma alloc_text (PAGE, Bus_EvtDeviceResourceRequirementsQuery);

WDF_PDO_EVENT_CALLBACKS pdoCallbacks;
WDF_PDO_EVENT_CALLBACK_INIT(&pdoCallbacks);

pdoCallbacks.EvtDeviceResourcesQuery = Bus_EvtDeviceResourcesQuery;
pdoCallbacks.EvtDeviceResourceRequirementsQuery = Bus_EvtDeviceResourceRequirementsQuery;

WdfPdoInitSetEventCallbacks(pDeviceInit, &pdoCallbacks);

NTSTATUS Bus_EvtDeviceResourcesQuery(IN WDFDEVICE Device, IN WDFIRESLIST Resources)
{
    // assign the subdevice resources
}

NTSTATUS Bus_EvtDeviceResourceRequirementsQuery(IN WDFDEVICE, IN WDFIORESREQLIST IoResourceRequirementsList)
{
    // assign the subdevice resources
}

// note that: the logic of the above two functions are almost the same
```

## For Sub-device Driver

the subdevice should register the callback funtion prepareHardware like the bus driver, then do the additional step:

``` c

EVT_WDF_DEVICE_REMOVE_ADDED_RESOURCES Bus_EvtDeviceRemoveAddedResources;

NTSTATUS xxxEvtDevicdAdd(IN WDFDRIVER Driver, IN PWDFDEVICE_INIT DeviceInit)
{
    ....

    WDF_FDO_EVENT_CALLBACKS fdoCallbacks;

    WDF_FDO_EVENT_CALLBACKS_INIT(&fdoCallbacks);
    fdoCallbacks.EvtDeviceRemoveAddedResources = Bus_EvtDeviceRemoveAddedResources;
    WdfFdoInitSetEventCallbacks(DeviceInit, &fdoCallbacks);
    ....
}

NTSTATUS Bus_EvtDeviceRemoveAddedResources(IN WDFDEVICE Device, IN WDFCMRESLIST ResourcesRaw, IN WDFCMRESLIST ResourcesTranslated)
{
    // just use WdfCmResourceListRemove(ResourcesRaw, i) and WdfCmResourceListRemove(ResourcesTranslated, i)
    // to remove the subdevice's i-th resource so that the bus device will not use the resources.
}

```

## inf example
```
;
; test.inf
;整个配置文件安装成功后会在注册表生成一个实例子健 具体位置为：HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Enum

;--------- Version Section ---------------------------------------------------

[Version]

;可以是$Chicage$、$Windows NT$(含有一个空格)、$Windows 95$(含有一个空格)之一，定界符$必不可少，且这些串是不分大小写的。
;如果Signature的值不是这些有效的串之一，该INF文件就被认为无效
Signature="$WINDOWS NT$"  

;INF文件的提供者
Provider=drsn_Device  
  
;INF文件的版本信息，时间和版本不变的情况下，修改了SYS文件，重新安装INF文件是看不到SYS变化的
;因为系统已经存根了此版本的INF和SYS,它会直接加载已有的文件,调试SYS特别要主要这个问题
;如果使用VS2012编译,它会自动帮你填写这里,比较省心
DriverVer=08/31/2013,8.33.48.258
                  
;如果设备是一个标准类别，使用标准类的名称和GUID 否则创建一个自定义的类别名称，并自定义它的GUID
;自定义的类别在注册表中 HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Class\ 有显示
Class=drsnDevice                  
ClassGuid={BDC0EAC4-AC4B-46af-82EA-C4958B686515}    
                          

;--------- SourceDiskNames and SourceDiskFiles Section -----------------------

;这里两项的设置效果是 加载INF当前目录下的SYS文件
[SourceDisksNames]          
1 = %DiskName%,,,       

[SourceDisksFiles]
Name_Files_Driver = 1,,       


;--------- ClassInstall/ClassInstall32 Section -------------------------------

;如果不是标准类别设备，这里的配置必须的
[ClassInstall32]          
Addreg=Class_AddReg

;对应的注册表是 HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Class\
[Class_AddReg]  
HKR,,,,%DeviceClassName%      
HKR,,Icon,,"-5" 


;--------- DestinationDirs Section -------------------------------------------

;把文件呢复制到相应的目录下，在win2000及其以后系统，12表示%windir%/system32/drivers 
;win98中12表示%windir%/system/IoSubsys 所以为了兼容大家都写成10,System32\Drivers
[DestinationDirs]         
Name_Files_Driver = 12        


;--------- Manufacturer and Models Sections ----------------------------------  

;这里是设置模型相关的选项，注意这里VS默认生成的标准设备的配置 如：%ManufacturerName%=Standard,NT$ARCH$
;如果不是标准类别设备这里必须修改，要不然最后加载的时候会出现259错误
[Manufacturer]
%ManufacturerName%=Mfg0       
        
;这里是模型节的节名，和硬件ID 这个ID可以自定义          
[Mfg0]
%DeviceDesc%=SysInstall, PCI\VEN_8888&DEV_8888    


;---------- DDInstall Sections -----------------------------------------------

;这里需要注意WIN2000及其以上的系统这里有个.NT，如果是98这里是[SysInstall]，必须要正确设置
[SysInstall.NT]           
CopyFiles=Name_Files_Driver  
AddReg=Install_NT_AddReg

;这里的drsnWDM是注册表中的服务名 具体地址是 HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services
[SysInstall.NT.Services]  
Addservice = drsnWDM, 0x00000002, Sys_AddService  

;服务的具体选项
[Sys_AddService]  
DisplayName = %SvcDesc%               
ServiceType = 1 ; SERVICE_KERNEL_DRIVER  
StartType = 3 ; SERVICE_DEMAND_START  
ErrorControl = 1 ; SERVICE_ERROR_NORMAL  

;这个地方虽然和[Name_Files_Driver]相同但是不能引用，所以只能照实来写
ServiceBinary = %12%\test.sys 

[Install_NT_AddReg]  
HKLM, "System\CurrentControlSet\Services\drsnWDM\Parameters",\  
"BreakOnEntry", 0x00010001, 0 

; --------- Files (common) -------------  

;sys文件名 便于配置文件其它地方使用
[Name_Files_Driver]                 
test.sys


;--------- Strings Section ---------------------------------------------------  

;字符串设置 便于配置文件其它地方使用
[Strings]                     
ProviderName="drsn" 
ManufacturerName="drsn soft"
DiskName="test Source Disk"
DeviceDesc="test protect" 
SvcDesc="drsn" 
DeviceClassName="drsn_Device"
```
