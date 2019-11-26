---
title: Learn to audit the contents of virtual machines
description: Learn how Azure Policy uses the Guest Configuration agent to audit settings inside virtual machines.
ms.date: 11/04/2019
ms.topic: conceptual
ms.openlocfilehash: f68bbc64ee8f0da02d213895a70e4c533b9a5f63
ms.sourcegitcommit: 95931aa19a9a2f208dedc9733b22c4cdff38addc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2019
ms.locfileid: "74463788"
---
# <a name="understand-azure-policys-guest-configuration"></a>Understand Azure Policy's Guest Configuration

Beyond auditing and [remediating](../how-to/remediate-resources.md) Azure resources, Azure Policy can audit settings inside a machine. A validação é executada pela extensão da Configuração de Convidado e pelo cliente. A extensão, através do cliente, valida as definições, como:

- The configuration of the operating system
- Presença ou configuração da aplicação
- Definições do ambiente

Neste momento, a Configuração de Convidado do Azure Policy audita apenas as definições no computador. Não se aplica às configurações.

## <a name="extension-and-client"></a>Extension and client

To audit settings inside a machine, a [virtual machine extension](../../../virtual-machines/extensions/overview.md) is enabled. The extension downloads applicable policy assignment and the corresponding configuration definition.

### <a name="limits-set-on-the-extension"></a>Limits set on the extension

To limit the extension from impacting applications running inside the machine, the Guest Configuration isn't allowed to exceed more than 5% of CPU utilization. This limitation exists for both built-in and custom definitions.

## <a name="register-guest-configuration-resource-provider"></a>Register Guest Configuration resource provider

Before you can use Guest Configuration, you must register the resource provider. You can register through the portal or through PowerShell. The resource provider is registered automatically if assignment of a Guest Configuration policy is done through the portal.

### <a name="registration---portal"></a>Registration - Portal

To register the resource provider for Guest Configuration through the Azure portal, follow these steps:

1. Launch the Azure portal and click on **All services**. Search for and select **Subscriptions**.

1. Find and click on the subscription that you want to enable Guest Configuration for.

1. In the left menu of the **Subscription** page, click **Resource providers**.

1. Filter for or scroll until you locate **Microsoft.GuestConfiguration**, then click **Register** on the same row.

### <a name="registration---powershell"></a>Registration - PowerShell

To register the resource provider for Guest Configuration through PowerShell, run the following command:

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell
Register-AzResourceProvider -ProviderNamespace 'Microsoft.GuestConfiguration'
```

## <a name="validation-tools"></a>Validation tools

Inside the machine, the Guest Configuration client uses local tools to run the audit.

The following table shows a list of the local tools used on each supported operating system:

|Sistema operativo|Validation tool|Notas|
|-|-|-|
|Windows|[Windows PowerShell Desired State Configuration](/powershell/scripting/dsc/overview/overview) v2| |
|Linux|[Chef InSpec](https://www.chef.io/inspec/)| Ruby and Python are installed by the Guest Configuration extension. |

### <a name="validation-frequency"></a>Validation frequency

The Guest Configuration client checks for new content every 5 minutes. Once a guest assignment is received, the settings are checked on a 15-minute interval. Results are sent to the Guest Configuration resource provider as soon as the audit completes. When a policy [evaluation trigger](../how-to/get-compliance-data.md#evaluation-triggers) occurs, the state of the machine is written to the Guest Configuration resource provider. This update causes Azure Policy to evaluate the Azure Resource Manager properties. An on-demand Azure Policy evaluation retrieves the latest value from the Guest Configuration resource provider. However, it doesn't trigger a new audit of the configuration within the machine.

## <a name="supported-client-types"></a>Supported client types

The following table shows a list of supported operating system on Azure images:

|Publicador|Nome|Versões|
|-|-|-|
|Canonical|Ubuntu Server|14.04, 16.04, 18.04|
|Credativ|Debian|8, 9|
|Microsoft|Windows Server|2012 Datacenter, 2012 R2 Datacenter, 2016 Datacenter, 2019 Datacenter|
|Microsoft|Cliente Windows|Windows 10|
|OpenLogic|CentOS|7.3, 7.4, 7.5|
|Red Hat|Red Hat Enterprise Linux|7.4, 7.5|
|Suse|SLES|12 SP3|

> [!IMPORTANT]
> Guest Configuration can audit nodes running a supported OS. If you would like to audit virtual machines that use a custom image, you need to duplicate the **DeployIfNotExists** definition and modify the **If** section to include your image properties.

### <a name="unsupported-client-types"></a>Unsupported client types

Windows Server Nano Server isn't supported in any version.

## <a name="guest-configuration-extension-network-requirements"></a>Guest Configuration Extension network requirements

To communicate with the Guest Configuration resource provider in Azure, machines require outbound access to Azure datacenters on port **443**. If you're using a private virtual network in Azure that doesn't allow outbound traffic, configure exceptions with [Network Security Group](../../../virtual-network/manage-network-security-group.md#create-a-security-rule) rules. A service tag doesn't currently exist for Azure Policy Guest Configuration.

For IP address lists, you can download [Microsoft Azure Datacenter IP Ranges](https://www.microsoft.com/download/details.aspx?id=41653). This file is updated weekly, and has the currently deployed ranges and any upcoming changes to the IP ranges. You only need to allow outbound access to the IPs in the regions where your VMs are deployed.

> [!NOTE]
> The Azure Datacenter IP address XML file lists the IP address ranges that are used in the Microsoft Azure datacenters. The file includes compute, SQL, and storage ranges. An updated file is posted weekly. The file reflects the currently deployed ranges and any upcoming changes to the IP ranges. New ranges that appear in the file aren't used in the datacenters for at least one week. It's a good idea to download the new XML file every week. Then, update your site to correctly identify services running in Azure. Azure ExpressRoute users should note that this file is used to update the Border Gateway Protocol (BGP) advertisement of Azure space in the first week of each month.

## <a name="guest-configuration-definition-requirements"></a>Guest Configuration definition requirements

Each audit run by Guest Configuration requires two policy definitions, a **DeployIfNotExists** definition and an **AuditIfNotExists** definition. The **DeployIfNotExists** definition is used to prepare the machine with the Guest Configuration agent and other components to support the [validation tools](#validation-tools).

The **DeployIfNotExists** policy definition validates and corrects the following items:

- Validate the machine has been assigned a configuration to evaluate. If no assignment is currently present, get the assignment and prepare the machine by:
  - Authenticating to the machine using a [managed identity](../../../active-directory/managed-identities-azure-resources/overview.md)
  - Installing the latest version of the **Microsoft.GuestConfiguration** extension
  - Installing [validation tools](#validation-tools) and dependencies, if needed

If the **DeployIfNotExists** assignment is Non-compliant, a [remediation task](../how-to/remediate-resources.md#create-a-remediation-task) can be used.

Once the **DeployIfNotExists** assignment is Compliant, the **AuditIfNotExists** policy assignment uses the local validation tools to determine if the configuration assignment is Compliant or Non-compliant. The validation tool provides the results to the Guest Configuration client. The client forwards the results to the Guest Extension, which makes them available through the Guest Configuration resource provider.

Azure Policy uses the Guest Configuration resource providers **complianceStatus** property to report compliance in the **Compliance** node. For more information, see [getting compliance data](../how-to/get-compliance-data.md).

> [!NOTE]
> The **DeployIfNotExists** policy is required for the **AuditIfNotExists** policy to return results. Without the **DeployIfNotExists**, the **AuditIfNotExists** policy shows "0 of 0" resources as status.

All built-in policies for Guest Configuration are included in an initiative to group the definitions for use in assignments. The built-in initiative named _\[Preview\]: Audit Password security settings inside Linux and Windows machines_ contains 18 policies. There are six **DeployIfNotExists** and **AuditIfNotExists** pairs for Windows and three pairs for Linux. The [policy definition](definition-structure.md#policy-rule) logic validates that only the target operating system is evaluated.

#### <a name="auditing-operating-system-settings-following-industry-baselines"></a>Auditing operating system settings following industry baselines

One of the initiatives available in Azure Policy provides the ability to audit operating system settings inside virtual machines following a "baseline" from Microsoft. The definition, _\[Preview\]: Audit Windows VMs that do not match Azure security baseline settings_ includes a complete set of audit rules based on settings from Active Directory Group Policy.

Most of the settings are available as parameters. This functionality allows you to customize what is audited to align the policy with your organizational requirements or to map the policy to third party information such as industry regulatory standards.

Some parameters support an integer value range. For example, the Maximum Password Age parameter can be set using a range operator to give flexibility to machine owners. You could audit that the effective Group Policy setting requiring users to change their passwords should be no more than 70 days, but shouldn't be less than one day. As described in the info-bubble for the parameter, to make this business policy the effective audit value, set the value to "1,70".

If you assign the policy using an Azure Resource Manager deployment template, you can use a parameters file to manage these settings from source control. Using a tool such as Git to manage changes to Audit policies with comments at each check-in documents evidence as to why an assignment should be an exception to the expected value.

#### <a name="applying-configurations-using-guest-configuration"></a>Applying configurations using Guest Configuration

The latest feature of Azure Policy configures settings inside machines. The definition _Configure the time zone on Windows machines_ makes changes to the machine by configuring the time zone.

When assigning definitions that begin with _Configure_, you must also assign the definition _Deploy prerequisites to enable Guest Configuration Policy on Windows VMs_. You can combine these definitions in an initiative if you choose.

#### <a name="assigning-policies-to-machines-outside-of-azure"></a>Assigning policies to machines outside of Azure

The Audit policies available for Guest Configuration include the **Microsoft.HybridCompute/machines** resource type. Any machines onboarded to [Azure Arc for Servers](../../../azure-arc/servers/overview.md) that are in the scope of the policy assignment are automatically included.

### <a name="multiple-assignments"></a>Multiple assignments

Guest Configuration policies currently only support assigning the same Guest Assignment once per machine, even if the Policy assignment uses different parameters.

## <a name="built-in-resource-modules"></a>Built-in resource modules

When installing the Guest Configuration extension, the 'GuestConfiguration' PowerShell module is included with the latest version of DSC resource modules. This module can be downloaded from the PowerShell Gallery by using the 'Manual Download' link from the module page [GuestConfiguration](https://www.powershellgallery.com/packages/GuestConfiguration/). The '.nupkg' file format can be renamed to '.zip' to uncompress and review.

## <a name="client-log-files"></a>Client log files

The Guest Configuration extension writes log files to the following locations:

Windows: `C:\Packages\Plugins\Microsoft.GuestConfiguration.ConfigurationforWindows\<version>\dsc\logs\dsc.log`

Linux: `/var/lib/waagent/Microsoft.GuestConfiguration.ConfigurationforLinux-<version>/GCAgent/logs/dsc.log`

Where `<version>` refers to the current version number.

### <a name="collecting-logs-remotely"></a>Collecting logs remotely

The first step in troubleshooting Guest Configuration configurations or modules should be to use the `Test-GuestConfigurationPackage` cmdlet following the steps in [Test a Guest Configuration package](../how-to/guest-configuration-create.md#test-a-guest-configuration-package).
If that isn't successful, collecting client logs can help diagnose issues.

#### <a name="windows"></a>Windows

To use the Azure VM Run Command capability to capture information from log files in Windows machines, the following example PowerShell script can be helpful. For more information, see [Run PowerShell scripts in your Windows VM with Run Command](../../../virtual-machines/windows/run-command.md).

```powershell
$linesToIncludeBeforeMatch = 0
$linesToIncludeAfterMatch = 10
$latestVersion = Get-ChildItem -Path 'C:\Packages\Plugins\Microsoft.GuestConfiguration.ConfigurationforWindows\' | ForEach-Object {$_.FullName} | Sort-Object -Descending | Select-Object -First 1
Select-String -Path "$latestVersion\dsc\logs\dsc.log" -pattern 'DSCEngine','DSCManagedEngine' -CaseSensitive -Context $linesToIncludeBeforeMatch,$linesToIncludeAfterMatch | Select-Object -Last 10
```

#### <a name="linux"></a>Linux

To use the Azure VM Run Command capability to capture information from log files in Linux machines, the following example Bash script can be helpful. For more information, see [Run shell scripts in your Linux VM with Run Command](../../../virtual-machines/linux/run-command.md)

```Bash
linesToIncludeBeforeMatch=0
linesToIncludeAfterMatch=10
latestVersion=$(find /var/lib/waagent/ -type d -name "Microsoft.GuestConfiguration.ConfigurationforLinux-*" -maxdepth 1 -print | sort -z | sed -n 1p)
egrep -B $linesToIncludeBeforeMatch -A $linesToIncludeAfterMatch 'DSCEngine|DSCManagedEngine' "$latestVersion/GCAgent/logs/dsc.log" | tail
```

## <a name="guest-configuration-samples"></a>Guest Configuration samples

Samples for Policy Guest Configuration are available in the following locations:

- [Samples index - Guest Configuration](../samples/index.md#guest-configuration)
- [Azure Policy samples GitHub repo](https://github.com/Azure/azure-policy/tree/master/samples/GuestConfiguration)

## <a name="next-steps"></a>Passos seguintes

- Review examples at [Azure Policy samples](../samples/index.md).
- Reveja a [estrutura de definição do Azure Policy](definition-structure.md).
- Veja [Compreender os efeitos do Policy](effects.md).
- Understand how to [programmatically create policies](../how-to/programmatically-create.md).
- Learn how to [get compliance data](../how-to/get-compliance-data.md).
- Learn how to [remediate non-compliant resources](../how-to/remediate-resources.md).
- Review what a management group is with [Organize your resources with Azure management groups](../../management-groups/overview.md).
