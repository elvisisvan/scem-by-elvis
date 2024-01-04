# scom
*system center operations manager*

https://learn.microsoft.com/en-us/system-center/scom

![basic][om2016-basic-management-group.png]

operations manager: monitor devices and software

operations console: check health, performance, availability; help identify + resolve problems

## services
scom has 3 major services: system center management service, system center data access service (scda), system center management configuration service (sccm)
- scda: enable opsmgr access to operational database and writes data to database
- sccm: enable opsmgr access to relationships and topology of management group + distributes management packs
## setup and deployment

**management group (mg)**: basic unit of functionality in operations manager suite which includes:
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
builds and presents reports from data in the data warehouse database

---
### additional ms
**gateway server**: monitor untrusted domains

---
### agents
a service installed on computer, collects data, creates alert, runs responses, monitor health state, report to ms
**proxy agent**: forward data to a management server on behalf of a computer or network device

---
### databases
includes: operational database, data warehouse database, audit collection services (acs) database
- operational database (od): contains all config data & stores monitoring data collected and processed, all for management group; short-term (7 days)
- data warehouse database (dwd): is for historical purposes, stores monitoring & alerting data & always contain current data; long-term
- acs database: collects logs helpful to inspect trends and conduct security analyses

---
### system requirements
https://learn.microsoft.com/en-us/system-center/scom/system-requirements
#### hardware
lowest minimum specs: 4-core 2.66 ghz + 8 gb ram + 10 gb disk space
highest minimum specs: 8-core 2.66 ghz + 32 gb ram + 10 gb disk space
#### software: 
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
### running services on management server


---
### resource pools
- 3 members = high availability
- high availability: no loss of monitoring when a member is unavailable
- quorum algorithm: more than 50% of members available to maintain high availability
- roles:
	- members: management server(ms) or gateway server (gs)
	- observers: same as members, no workflows participation, yes quorum decisions participation, rarely used 
	- default observer (deob): opsmgr database, enabled by default, allow high availability for pools with less than 3 members
- scenarios:
	- management server:
		- single ms: no high availability, single point of failure, deob gives no benefit
		- 2 ms: high availability, lose highava if disable deob
		- 3 ms: highava, can only have max 1 ms down, deob gives no value
		- 4 ms: highava, max 2 ms down => deob has great value
		- >4 ms: highava, deob has no value => should be removed
	- gateway server:
		- use cases: local agentless coms across small wan circuit, monitor unix/linux servers in a firewalled off dmz (de-militarized zone)
		- deob should not be used because gateways do not have local sdk services => cannot query database
	- 2 gs + 1 observer:
		- rare cases with specific firewall rules need workaround
		- requires tcp_57523 opened for health service coms
	- agents-only:
		- possible but not officially supported by microsoft (supports only ms and gateways)
		- can only be set up via terminal (powershell)

reference: [Understanding SCOM Resource Pools – Kevin Holman's Blog](https://kevinholman.com/2016/11/21/understanding-scom-resource-pools/)

---
### disaster recovery
- protect from system failure, system loss
- not optimized for accidental, unintended, malicious data loss or corruption
- example operations to restore: low-priority reporting database, analysis data
- multisite dr at system/application level expense is much greater than data's value in many cases 
- recovery point objective (rpo): tolerance of extent of monitoring data loss
- recovery time objective (rto): level of complexity and expense
	- 2 common dr options:
		- duplication: similar in scale and configuration to primary mg, no tolerance for downtime, most complex, includes integration with itsm platforms (scsm, remedy, servicenow, etc.), data duplication
		- secondary deployment: in cold-standby config, no participation in mg until dr is triggered
	- alternatives:
		- deploy additional mg components: to retain functionalities of mg, minimum implementation: sql server 2014/2016 always on availability group for operational & data warehouse databases, two-node failover cluster instance (FCI) in primary datacenter, standalone sql server  in secondary datacenter as part of wsfc (windows server failover cluster)
![simple][om2016-dr-simple-config-expanded.png]

		- azure virtual machine: require sql server, set up configurations as described above


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
**back up registry before edit**
- should be enabled for all incoming/outcoming coms
- 2 methods to configure system to only use tls 1.2 protocol: manual and automatic registry modification
- for windows os:
	- manual: 
		1. windows run `regedit` 
		2. locate registry subkey `HKEY_LOCAL_MACHINE\System\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols` 
		3. create **Protocols** subkey for ssl 2.0, ssl 3.0, tls 1.0, tls 1.1, tls 1.2
		4. create client and server subkey under each protocol
		5. create DWORD values under each protocol to enable/disable them: 
			- **Enabled** [Value = 0]
		    - **DisabledByDefault** [Value = 1]
		    - or
			- **Enabled** [Value = 1]
		    - **DisabledByDefault** [Value = 0]
	- automatic:
		```powershell
		$ProtocolList       = @("SSL 2.0", "SSL 3.0", "TLS 1.0", "TLS 1.1", "TLS 1.2")
		$ProtocolSubKeyList = @("Client", "Server")
		$DisabledByDefault  = "DisabledByDefault"
		$registryPath       = "HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\"
		
		foreach ($Protocol in $ProtocolList)
		{
			foreach ($key in $ProtocolSubKeyList)
			{
				$currentRegPath = $registryPath + $Protocol + "\" + $key
				Write-Output "Current Registry Path: `"$currentRegPath`""
		
				if (!(Test-Path $currentRegPath))
				{
					Write-Output " `'$key`' not found: Creating new Registry Key"
					New-Item -Path $currentRegPath -Force | out-Null
				}
				if ($Protocol -eq "TLS 1.2")
				{
					Write-Output " Enabling - TLS 1.2"
					New-ItemProperty -Path $currentRegPath -Name $DisabledByDefault -Value "0" -PropertyType DWORD -Force | Out-Null
					New-ItemProperty -Path $currentRegPath -Name 'Enabled' -Value "1" -PropertyType DWORD -Force | Out-Null
				}
				else
				{
					Write-Output " Disabling - $Protocol"
					New-ItemProperty -Path $currentRegPath -Name $DisabledByDefault -Value "1" -PropertyType DWORD -Force | Out-Null
					New-ItemProperty -Path $currentRegPath -Name 'Enabled' -Value "0" -PropertyType DWORD -Force | Out-Null
				}
				Write-Output " "
			}
		}
		```
- for operations manager:
	- manual:
		1. windows run `regedit`
		2. locate subkey `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319`
		3. DWORD value **SchUseStrongCrypto** with value **1**
		4. locate subkey `HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319`
		5. DWORD value **SchUseStrongCrypto** with value **1**
		6. restart system
	- automatic:
		```powershell
		# Tighten up the .NET Framework
		$NetRegistryPath = "HKLM:\SOFTWARE\Microsoft\.NETFramework\v4.0.30319"
		New-ItemProperty -Path $NetRegistryPath -Name "SchUseStrongCrypto" -Value "1" -PropertyType DWORD -Force | Out-Null
		
		$NetRegistryPath = "HKLM:\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319"
		New-ItemProperty -Path $NetRegistryPath -Name "SchUseStrongCrypto" -Value "1" -PropertyType DWORD -Force | Out-Null
		```
- acs (audit collection services): update **dsn** settings on acs collector server for tls 1.2 to work because acs uses dsn to connect to db
	1. windows run `regedit`
	2. locate subkey `HKEY_LOCAL_MACHINE\SOFTWARE\ODBC\ODBC.INI\OpsMgrAC`
	3. change dsn named "OpsMgrAC" to "ODBC Driver 17 for SQL Server"
	4. change driver entry to `%WINDIR%\system32\msodbcsql17.dll`
	- alternative methods: 
		1. create new .reg file and execute it, file name: ODBC 17.reg
		```powershell
			Windows Registry Editor Version 5.00
		
		[HKEY_LOCAL_MACHINE\SOFTWARE\ODBC\ODBC.INI\ODBC Data Sources]
		"OpsMgrAC"="ODBC Driver 17 for SQL Server"
		
		[HKEY_LOCAL_MACHINE\SOFTWARE\ODBC\ODBC.INI\OpsMgrAC]
		"Driver"="%WINDIR%\system32\msodbcsql17.dll"
		```
		2. terminal commands:
		```powershell
		New-ItemProperty -Path "HKLM:\SOFTWARE\ODBC\ODBC.INI\OpsMgrAC" -Name "Driver" -Value "%WINDIR%\system32\msodbcsql7.dll" -PropertyType STRING -Force | Out-Null
		New-ItemProperty -Path "HKLM:\SOFTWARE\ODBC\ODBC.INI\ODBC Data Sources" -Name "OpsMgrAC" -Value "ODBC Driver 17 for SQL Server" -PropertyType STRING -Force | Out-Null
		```
reference: [Implement TLS 1.2 for Operations Manager | Microsoft Learn](https://learn.microsoft.com/en-us/system-center/scom/plan-security-tls12-config?view=sc-om-2019)


---
## OMS vs. OM
- oms is suitable when beginning to extend a small single-server network
- oms is meant to compliment and extend what om can do for parge enterprise environments, not to replace
- oms can be used independently to provide log analytics and monitoring controls for small and medium-sized environments with multi-server data centers/deployments 
- does not require systems center
- cloud-based management solution for on-prem servers & PM 
- OMS tools:
	- Solution Paths
	- Active Directory
	- Active Directory Assessments
	- Replication Health
	- Upgrade Readiness

# scsm
*system center service manager*
6 major parts: service manager management server, server manager database, data warehouse management server, data warehouse databases and service manager console
3 required accounts: management group administrators, service manager services account and workflow account
3-tiered application consist of a database, a data access module and a console

---
---
# orchestrator



---
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
dwd is for historical purposes, stores monitoring & alerting data & always contain current data; long-term

## difference between rules and monitoring?
rules: defines the events and performance data to collect from computers and what to do with the information after collected, simply understand, it's if/then statement
monitors: define health state for particular aspects of the monitored object; can be configured to generate an laert when a state change occurs

## reporting server


## gateway server
gateway server: enables the monitoring of computers in untrusted domains

## proxy agent vs regular agent
agentless monitoring: a proxy (remote) agent is an agent that can forward data to a management server on behalf of a computer or network device other than its host computer; 
agent monitoring: 
