---
title: Understand the Windows agent check results in Azure Update Management
description: Learn how to troubleshoot issues with the Update Management agent.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 11/25/2019
ms.topic: conceptual
ms.service: automation
ms.subservice: update-management
manager: carmonm
ms.openlocfilehash: 72fdfe912a5560ce0c0e3886dd3c56cf9534dc22
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2019
ms.locfileid: "74480790"
---
# <a name="understand-the-windows-agent-check-results-in-update-management"></a>Understand the Windows agent check results in Update Management

There may be many reasons your machine isn't showing **Ready** in Update Management. In Update Management, you can check the health of a Hybrid Worker agent to determine the underlying problem. This article discusses how to run the troubleshooter for Azure machines from the Azure portal and Non-Azure machines in the [offline scenario](#troubleshoot-offline).

The following list are the three readiness states a machine can be in:

* **Ready** - The update agent is deployed and was last seen less than 1 hour ago.
* **Disconnected** -  The update agent is deployed and was last seen over 1 hour ago.
* **Not configured** -  The update agent isn't found or hasn't finished onboarding.

> [!NOTE]
> There may be a slight delay between what the Azure portal shows and the current state of the machine.

## <a name="start-the-troubleshooter"></a>Start the troubleshooter

For Azure machines, clicking the **Troubleshoot** link under the **Update Agent Readiness** column in the portal launches the **Troubleshoot Update Agent** page. For Non-Azure machines, the link brings you to this article. See the [offline instructions](#troubleshoot-offline) to troubleshoot a Non-Azure machine.

![Update management list of virtual machines](../media/update-agent-issues/vm-list.png)

> [!NOTE]
> To check the health of an agent, the VM must be running. If the VM isn't running, a **Start the VM** button appears.

On the **Troubleshoot Update Agent** page, select **Run checks** to start the troubleshooter. The troubleshooter uses [Run Command](../../virtual-machines/windows/run-command.md) to run a script on the machine to verify agent dependencies. When the troubleshooter is finished, it returns the result of the checks.

![Troubleshoot Update Agent page](../media/update-agent-issues/troubleshoot-page.png)

Results are shown on the page when they're ready. The checks sections show what's included in each check.

![Troubleshoot Update Agent checks](../media/update-agent-issues/update-agent-checks.png)

## <a name="prerequisite-checks"></a>Prerequisite checks

### <a name="operating-system"></a>Sistema operativo

The operating system check verifies whether the Hybrid Runbook Worker is running one of these operating systems:

|Sistema operativo  |Notas  |
|---------|---------|
|Windows Server 2008 R2 RTM, Windows Server 2008 | Supports only update assessments.         |
|Windows Server 2008 R2 SP1 and later |.NET Framework 4.6 or later is required. ([Download the .NET Framework](/dotnet/framework/install/guide-for-developers))<br/> Windows PowerShell 5.1 is required.  ([Download Windows Management Framework 5.1](https://www.microsoft.com/download/details.aspx?id=54616))        |

### <a name="net-462"></a>.NET 4.6.2

The .NET Framework check verifies that the system has a minimum of [.NET Framework 4.6.2](https://www.microsoft.com/en-us/download/details.aspx?id=53345) installed.

### <a name="wmf-51"></a>WMF 5.1

The WMF check verifies that the system has the required version of the Windows Management Framework (WMF) - [Windows Management Framework 5.1](https://www.microsoft.com/download/details.aspx?id=54616).

### <a name="tls-12"></a>TLS 1.2

This check determines whether you're using TLS 1.2 to encrypt your communications. TLS 1.0 is no longer supported by the platform. We recommend that clients use TLS 1.2 to communicate with Update Management.

## <a name="connectivity-checks"></a>Connectivity checks

### <a name="registration-endpoint"></a>Registration endpoint

This check determines whether the agent can properly communicate with the agent service.

Proxy and firewall configurations must allow the Hybrid Runbook Worker agent to communicate with the registration endpoint. For a list of addresses and ports to open, see [Network planning for Hybrid Workers](../automation-hybrid-runbook-worker.md#network-planning).

### <a name="operations-endpoint"></a>Operations endpoint

This check determines whether the agent can properly communicate with the Job Runtime Data Service.

Proxy and firewall configurations must allow the Hybrid Runbook Worker agent to communicate with the Job Runtime Data Service. For a list of addresses and ports to open, see [Network planning for Hybrid Workers](../automation-hybrid-runbook-worker.md#network-planning).

## <a name="vm-service-health-checks"></a>VM service health checks

### <a name="monitoring-agent-service-status"></a>Monitoring agent service status

This check determines whether `HealthService`, the Microsoft Monitoring Agent, is running on the machine.

To learn more about troubleshooting the service, see [The Microsoft Monitoring Agent is not running](hybrid-runbook-worker.md#mma-not-running).

To reinstall the Microsoft Monitoring Agent, see [Install and configure the Microsoft Monitoring Agent](../../azure-monitor/learn/quick-collect-windows-computer.md#install-the-agent-for-windows).

### <a name="monitoring-agent-service-events"></a>Monitoring agent service events

This check determines whether any `4502` events appear in the Azure Operations Manager log on the machine in the past 24 hours.

To learn more about this event, see the [troubleshooting guide](hybrid-runbook-worker.md#event-4502) for this event.

## <a name="access-permissions-checks"></a>Access permissions checks

### <a name="machinekeys-folder-access"></a>MachineKeys folder access

The Crypto folder access check determines whether the Local System Account has access to C:\ProgramData\Microsoft\Crypto\RSA.

## <a name="troubleshoot-offline"></a>Troubleshoot offline

You can use the troubleshooter on a Hybrid Runbook Worker offline by running the script locally. You can get the script,  [Troubleshoot-WindowsUpdateAgentRegistration](https://www.powershellgallery.com/packages/Troubleshoot-WindowsUpdateAgentRegistration), in the PowerShell Gallery. You must have WMF 4.0, or greater, installed to run the script. To download the latest version of PowerShell, see [Installing various versions of PowerShell](https://docs.microsoft.com/powershell/scripting/install/installing-powershell).

The output of this script looks like the following example:

```output
RuleId                      : OperatingSystemCheck
RuleGroupId                 : prerequisites
RuleName                    : Operating System
RuleGroupName               : Prerequisite Checks
RuleDescription             : The Windows Operating system must be version 6.1.7601 (Windows Server 2008 R2 SP1) or higher
CheckResult                 : Passed
CheckResultMessage          : Operating System version is supported
CheckResultMessageId        : OperatingSystemCheck.Passed
CheckResultMessageArguments : {}

RuleId                      : DotNetFrameworkInstalledCheck
RuleGroupId                 : prerequisites
RuleName                    : .NET Framework 4.5+
RuleGroupName               : Prerequisite Checks
RuleDescription             : .NET Framework version 4.5 or higher is required
CheckResult                 : Passed
CheckResultMessage          : .NET Framework version 4.5+ is found
CheckResultMessageId        : DotNetFrameworkInstalledCheck.Passed
CheckResultMessageArguments : {}

RuleId                      : WindowsManagementFrameworkInstalledCheck
RuleGroupId                 : prerequisites
RuleName                    : WMF 5.1
RuleGroupName               : Prerequisite Checks
RuleDescription             : Windows Management Framework version 4.0 or higher is required (version 5.1 or higher is preferable)
CheckResult                 : Passed
CheckResultMessage          : Detected Windows Management Framework version: 5.1.17763.1
CheckResultMessageId        : WindowsManagementFrameworkInstalledCheck.Passed
CheckResultMessageArguments : {5.1.17763.1}

RuleId                      : AutomationAgentServiceConnectivityCheck1
RuleGroupId                 : connectivity
RuleName                    : Registration endpoint
RuleGroupName               : connectivity
RuleDescription             :
CheckResult                 : Failed
CheckResultMessage          : Unable to find Workspace registration information in registry
CheckResultMessageId        : AutomationAgentServiceConnectivityCheck1.Failed.NoRegistrationFound
CheckResultMessageArguments : {}

RuleId                      : AutomationJobRuntimeDataServiceConnectivityCheck
RuleGroupId                 : connectivity
RuleName                    : Operations endpoint
RuleGroupName               : connectivity
RuleDescription             : Proxy and firewall configuration must allow Automation Hybrid Worker agent to communicate with eus2-jobruntimedata-prod-su1.azure-automation.net
CheckResult                 : Passed
CheckResultMessage          : TCP Test for eus2-jobruntimedata-prod-su1.azure-automation.net (port 443) succeeded
CheckResultMessageId        : AutomationJobRuntimeDataServiceConnectivityCheck.Passed
CheckResultMessageArguments : {eus2-jobruntimedata-prod-su1.azure-automation.net}

RuleId                      : MonitoringAgentServiceRunningCheck
RuleGroupId                 : servicehealth
RuleName                    : Monitoring Agent service status
RuleGroupName               : VM Service Health Checks
RuleDescription             : HealthService must be running on the machine
CheckResult                 : Failed
CheckResultMessage          : Microsoft Monitoring Agent service (HealthService) is not running
CheckResultMessageId        : MonitoringAgentServiceRunningCheck.Failed
CheckResultMessageArguments : {Microsoft Monitoring Agent, HealthService}

RuleId                      : MonitoringAgentServiceEventsCheck
RuleGroupId                 : servicehealth
RuleName                    : Monitoring Agent service events
RuleGroupName               : VM Service Health Checks
RuleDescription             : Event Log must not have event 4502 logged in the past 24 hours
CheckResult                 : Failed
CheckResultMessage          : Microsoft Monitoring Agent service Event Log (Operations Manager) does not exist on the machine
CheckResultMessageId        : MonitoringAgentServiceEventsCheck.Failed.NoLog
CheckResultMessageArguments : {Microsoft Monitoring Agent, Operations Manager, 4502}

RuleId                      : CryptoRsaMachineKeysFolderAccessCheck
RuleGroupId                 : permissions
RuleName                    : Crypto RSA MachineKeys Folder Access
RuleGroupName               : Access Permission Checks
RuleDescription             : SYSTEM account must have WRITE and MODIFY access to 'C:\\ProgramData\\Microsoft\\Crypto\\RSA\\MachineKeys'
CheckResult                 : Passed
CheckResultMessage          : Have permissions to access C:\\ProgramData\\Microsoft\\Crypto\\RSA\\MachineKeys
CheckResultMessageId        : CryptoRsaMachineKeysFolderAccessCheck.Passed
CheckResultMessageArguments : {C:\\ProgramData\\Microsoft\\Crypto\\RSA\\MachineKeys}

RuleId                      : TlsVersionCheck
RuleGroupId                 : prerequisites
RuleName                    : TLS 1.2
RuleGroupName               : Prerequisite Checks
RuleDescription             : Client and Server connections must support TLS 1.2
CheckResult                 : Passed
CheckResultMessage          : TLS 1.2 is enabled by default on the Operating System.
CheckResultMessageId        : TlsVersionCheck.Passed.EnabledByDefault
CheckResultMessageArguments : {}
```

## <a name="next-steps"></a>Passos seguintes

To troubleshoot more issues with your Hybrid Runbook Workers, see [Troubleshoot Hybrid Runbook Workers](hybrid-runbook-worker.md).

