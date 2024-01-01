# scom
*system center operations manager*
https://learn.microsoft.com/en-us/system-center/scom
![[om2016-basic-management-group.png]]

operations manager: monitor devices and software

operations console: check health, performance, availability; help identify + resolve problems

## setup and deployment

management group (mg): basic unit of functionality in operations manager suite including:
### management servers (ms)

master admin, communicate with agents, databases


---
### consoles: operations vs. web

operations console: 
+ manage alerts & monitor data
+ manage and edit monitoring config
+ generate and view reports
+ administer management group settings
+ build customized workspaces

web console:
+ does not have full functionality of ops console
+ has access to all monitoring data and tasks on computers monitored from ops console
+ has access to *monitoring & workspace* views


---
### reporting server


---
### additional ms
**gateway server**: monitor untrusted domains

---
### databases


---
### system requirements
https://learn.microsoft.com/en-us/system-center/scom/system-requirements
#### hardware
lowest minimum specs: 4-core 2.66 ghz + 8 gb ram + 10 gb disk space
highest minimum specs: 8-core 2.66 ghz + 32 gb ram + 10 gb disk space
### software: 
1. server
+ windows server 2019 standard, datacenter

2. opsmgr operational, data warehouse, acs audit database
+ [os](https://learn.microsoft.com/en-us/system-center/scom/system-requirements?view=sc-om-2022#server-operating-system)
+ [sql server](https://learn.microsoft.com/en-us/system-center/scom/plan-sqlserver-design?view=sc-om-2022#sql-server-requirements)

4. management/gateway server: 
+ powershell 2.0
+ windows remote management enabled
+ .net 4.7.2

5. web console
+ internet explorer 11
+ microsoft edge 88
+ google chrome 88
+ iis (internet information services) 7.5
+ selected site for web console with configured http or https binding
+ .net 4.7.2

6. opsmgr reporting server
+ [os](https://learn.microsoft.com/en-us/system-center/scom/system-requirements?view=sc-om-2022#server-operating-system)
+ [sql server](https://learn.microsoft.com/en-us/system-center/scom/plan-sqlserver-design?view=sc-om-2022#sql-server-requirements)
+ remote registry service: enabled and started
+ sql server reporting services
+ .net 4.7.2

7. client os
win10 or win11

8. microsoft monitoring agent 
+ .net 3.5+4.7.2 or higher
+ powershell 3.0
+ ntfs file system

10. virtualization
+ satisfy all above requirements except:
	+ there exists activities uncommitted to virtual hard drive (point-in-time snapshots, temp vhd)
	+ when any opsmgr component can't be paused or placed in 'save state'
+ system center 2016

11. supported coexistence
varies depend on scom version

12. in-place upgrade
varies depend on scom version


---
### deployment


---
### running services


---
### disaster recovery


---
## operations and web consoles

### how to connect to operations and web console


---
### exploring operations and web consoles


---
### run web console server on standalone server


---
## security

### ports 


---
### antivirus exclusions


---
### accounts and permissions during installation


---
### configure SPNs


---
### run-as accounts and profiles


---
### user roles


---
### TLS 1.2

---

# scsm
*system center service manager*
6 major parts: service manager management server, server manager database, data warehouse management server, data warehouse databases and service manager console
3 required accounts: management group administrators, service manager services account and workflow account
3-tiered application consist of a database, a data access module and a console

---

# scda
*system center data access*

---
# scmc
*system center management configuration*

---

# interview 
## what is scom?
scom - system center operation manager, is a crucial service within the microsoft system center suite providing infrastructure monitoring that is flexible and cost-effective, helps ensure the predictable performance and availability of vital applications, and offers comprehensive monitoring for datacenter and cloud, both private and public

## what is management group in scom? how does it work?
management group is a basic unit of functionality of operation manager, at minimum it consists of a *management server*, *operational database* and *reporting data warehouse database* (and an additional *reporting server* if reporting functionality is installed)

## services of scom? (verbose details)
services within scom are: management servers, agents, management packs, services (microsoft monitoring agent), 

## what is resource pooling in scom? how does it work?
resource pool is an pool consists of multiple management servers  spreading workloads across these servers; 
- newly added management servers are assigned some of the work from existing servers; 
- each member will manage a distinct set of remote objects 
- no 2 members of the same resource pool will manage the same object at the same time

## what if client cannot connect to console?
1. check health service: windows + r > services.msc > microsoft monitoring agent (health service) > double-click open properties panel > set *startup* type to *automatic* > click *start* if **service status** is not **started**
2. check antivirus exclusions: 
- exclusions by process executable: MonitoringHost.exe, HealthService.exe, MOMPerfSnapshotHelper.exe, Microsoft.Mom.Sdk.ServiceHost.exe, cshost.exe
- exclusions by directories: 
- exclusions of file type by extension: 
3. check network issues:
- agent computer must connect to tcp:5723
- ports must be enabled: tcp&udp :389 for ldap; tcp&udp :88 for kerberos authentication; tcp&udp :53 for dns
- rpc communications complete successfully
- netsh int ipv4 show dynamic

reference: https://learn.microsoft.com/en-us/troubleshoot/system-center/scom/troubleshoot-agent-connectivity-issues
## differences between operational database and data warehouse?
operational database (od) vs data warehouse database (dwd): 
od contains all config data & stores monitoring data collected and processed, all for management group; short-term (7 days)
wdw is for historical purposes, stores monitoring & alerting data & always contain current data; long-term

## difference between rules and monitoring?
rules: defines the events and performance data to collect from computers and what to do with the information after collected, simply understand, it's if/then statement
monitors: define health state for particular aspects of the monitored object; can be configured to generate an laert when a state change occurs

## reporting server


## gateway server
gateway server: enables the monitoring of computers in untrusted domains

## proxy agent vs regular agent
agentless monitoring: a proxy (remote) agent is an agent that can forward data to a management server on behalf of a computer or network device other than its host computer; 
agent monitoring: 
