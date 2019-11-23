---
title: Connect Windows computers to Azure Monitor | Microsoft Docs
description: This article describes how to connect Windows computers hosted in other clouds or on-premises to Azure Monitor with the Log Analytics agent for Windows.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: MGoedtel
ms.author: magoedte
ms.date: 10/07/2019
ms.openlocfilehash: 42183ca7b02ba75b241ee1a83b5a0dc936a8c1c8
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/23/2019
ms.locfileid: "74420414"
---
# <a name="connect-windows-computers-to-azure-monitor"></a>Connect Windows computers to Azure Monitor

In order to monitor and manage virtual machines or physical computers in your local datacenter or other cloud environment with Azure Monitor, you need to deploy the Log Analytics agent (also referred to as the Microsoft Monitoring Agent (MMA)) and configure it to report to one or more Log Analytics workspaces. The agent also supports the Hybrid Runbook Worker role for Azure Automation.  

On a monitored Windows computer, the agent is listed as the Microsoft Monitoring Agent service. The Microsoft Monitoring Agent service collects events from log files and Windows event log, performance data, and other telemetry. Even when the agent is unable to communicate with Azure Monitor it reports to, the agent continues to run and queues the collected data on the disk of the monitored computer. When the connection is restored, the Microsoft Monitoring Agent service sends collected data to the service.

The agent may be installed by using one of the following methods. Most installations use a combination of these methods to install different sets of computers, as appropriate.  Details on using each method are provided later in the article.

* Manual installation. Setup is manually run on the computer using the setup wizard, from the command line, or deployed using an existing software distribution tool.
* Azure Automation Desired State Configuration (DSC). Using DSC in Azure Automation with a script for Windows computers already deployed in your environment.  
* PowerShell script.
* Resource Manager template for virtual machines running Windows on-premises in Azure Stack. 

>[!NOTE]
>Azure Security Center (ASC) depends on the Microsoft Monitoring Agent (also referred to as the Log Analytics Windows agent) and will install and configure it to report to a Log Analytics workspace as part of its deployment. ASC includes an automatic provisioning option which enables automatic installation of the Log Analytics Windows agent on all VMs in your subscription and configures it to report to a specific workspace. For more information about this option, see [Enable automatic provisioning of Log Analytics agent](../../security-center/security-center-enable-data-collection.md#auto-provision-mma).
>

If you need to configure the agent to report to more than one workspace, this cannot be performed during initial setup, only afterwards by updating the settings from Control Panel or PowerShell as described in [Adding or removing a workspace](agent-manage.md#adding-or-removing-a-workspace).  

Para compreender a configuração suportada, consulte [sistemas operativos Windows suportados](log-analytics-agent.md#supported-windows-operating-systems) e [configuração de firewall de rede](log-analytics-agent.md#network-firewall-requirements).

## <a name="obtain-workspace-id-and-key"></a>Obter o ID e a chave da área de trabalho
Before installing the Log Analytics agent for Windows, you need the workspace ID and key for your Log Analytics workspace.  This information is required during setup from each installation method to properly configure the agent and ensure it can successfully communicate with Azure Monitor in Azure commercial and US Government cloud. 

1. No portal do Azure, clique em **Todos os serviços**. Na lista de recursos, escreva **Log Analytics**. À medida que começa a escrever, a lista filtra com base na sua entrada. Selecione **Log Analytics**.
2. In your list of Log Analytics workspaces, select the workspace you intend on configuring the agent to report to.
3. Selecione **Definições avançadas**.<br><br> ![Definições Avançadas do Log Analytics](media/agent-windows/log-analytics-advanced-settings-01.png)<br><br>  
4. Selecione **Origens Ligadas** e, em seguida, selecione **Servidores Windows**.   
5. Copy and paste into your favorite editor, the **Workspace ID** and **Primary Key**.    
   
## <a name="configure-agent-to-use-tls-12"></a>Configure Agent to use TLS 1.2
To configure use of the [TLS 1.2](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings#tls-12) protocol for communication between the Windows agent and the Log Analytics service, you can follow the steps below to enable before the agent is installed on the virtual machine or afterwards.

>[!NOTE]
>If you are configuring a VM running Windows Server 2008 SP2 x64 to use TLS 1.2, you first need to install the following [SHA-2 code signing support update](https://support.microsoft.com/help/4474419/sha-2-code-signing-support-update) before performing the steps below. 
>

1. Locate the following registry subkey: **HKEY_LOCAL_MACHINE\System\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols**
2. Create a subkey under **Protocols** for TLS 1.2 **HKLM\System\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2**
3. Create a **Client** subkey under the TLS 1.2 protocol version subkey you created earlier. For example, **HKLM\System\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client**.
4. Create the following DWORD values under **HKLM\System\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client**:

    * **Enabled** [Value = 1]
    * **DisabledByDefault** [Value = 0]  

Configure .NET Framework 4.6 or later to support secure cryptography, as by default it is disabled. The [strong cryptography](https://docs.microsoft.com/dotnet/framework/network-programming/tls#schusestrongcrypto) uses more secure network protocols like TLS 1.2, and blocks protocols that are not secure. 

1. Locate the following registry subkey: **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\\.NETFramework\v4.0.30319**.  
2. Create the DWORD value **SchUseStrongCrypto** under this subkey with a value of **1**.  
3. Locate the following registry subkey: **HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\\.NETFramework\v4.0.30319**.  
4. Create the DWORD value **SchUseStrongCrypto** under this subkey with a value of **1**. 
5. Restart the system for the settings to take effect. 

## <a name="install-the-agent-using-setup-wizard"></a>Install the agent using setup wizard
The following steps install and configure the Log Analytics agent in Azure and Azure Government cloud by using the setup wizard for the agent on your computer. If you want to learn how to configure the agent to also report to a System Center Operations Manager management group, see [deploy the Operations Manager agent with the Agent Setup Wizard](https://docs.microsoft.com/system-center/scom/manage-deploy-windows-agent-manually#to-deploy-the-operations-manager-agent-with-the-agent-setup-wizard).

1. In your Log Analytics workspace, from the **Windows Servers** page you navigated to earlier, select the appropriate **Download Windows Agent** version to download depending on the processor architecture of the Windows operating system.   
2. Execute a Configuração para instalar o agente no seu computador.
2. Na página **Bem-vindo**, clique em **Seguinte**.
3. Na página **Termos de Licenciamento**, leia a licença e clique em **Aceito**.
4. Na página **Pasta de Destino**, altere ou mantenha a pasta de instalação predefinida e clique em **Seguinte**.
5. Na página **Opções de Configuração do Agente** , escolha ligar o agente ao Azure Log Analytics e clique em **Seguinte**.   
6. Na página **Azure Log Analytics**, faça o seguinte:
   1. Cole o **ID da Área de Trabalho** e a **Chave da Área de Trabalho (Chave Primária)** que copiou anteriormente.  Caso o computador deva reportar a uma área de trabalho do Log Analytics na cloud do Azure Government, selecione **Azure US Government**, na lista pendente **Cloud do Azure**.  
   2. Se o computador tiver de comunicar através de um servidor proxy com o serviço do Log Analytics, clique em **Avançadas** e forneça o URL e o número da porta do servidor proxy.  Se o seu servidor proxy precisar de autenticação, escreva o nome de utilizador e a palavra-passe para autenticação no mesmo e clique em **Seguinte**.  
7. Depois de indicar as definições de configuração necessárias, clique em **Seguinte**.<br><br> ![colar o ID da Área de Trabalho e a Chave Primária](media/agent-windows/log-analytics-mma-setup-laworkspace.png)<br><br>
8. Na página **Pronto para Instalar**, reveja as suas opções e clique em **Instalar**.
9. Na página **Configuração Concluída com Êxito**, clique em **Concluir**.

Quando terminar, o **Microsoft Monitoring Agent** aparece no **Painel de Controlo**. To confirm it is reporting to Log Analytics, review [Verify agent connectivity to Log Analytics](#verify-agent-connectivity-to-log-analytics). 

## <a name="install-the-agent-using-the-command-line"></a>Install the agent using the command line
The downloaded file for the agent is a self-contained installation package.  The setup program for the agent and supporting files are contained in the package and need to be extracted in order to properly install using the command line shown in the following examples.    

>[!NOTE]
>If you want to upgrade an agent, you need to use the Log Analytics scripting API. See the topic [Managing and maintaining the Log Analytics agent for Windows and Linux](agent-manage.md) for further information.

The following table highlights the specific parameters supported by setup for the agent, including when deployed using Automation DSC.

|MMA-specific options                   |Notas         |
|---------------------------------------|--------------|
| NOAPM=1                               | Optional parameter. Installs the agent without .NET Application Performance Monitoring.|   
|ADD_OPINSIGHTS_WORKSPACE               | 1 = Configure the agent to report to a workspace                |
|OPINSIGHTS_WORKSPACE_ID                | Workspace ID (guid) for the workspace to add                    |
|OPINSIGHTS_WORKSPACE_KEY               | Workspace key used to initially authenticate with the workspace |
|OPINSIGHTS_WORKSPACE_AZURE_CLOUD_TYPE  | Specify the cloud environment where the workspace is located <br> 0 = Azure commercial cloud (default) <br> 1 = Azure Government |
|OPINSIGHTS_PROXY_URL               | URI for the proxy to use |
|OPINSIGHTS_PROXY_USERNAME               | Username to access an authenticated proxy |
|OPINSIGHTS_PROXY_PASSWORD               | Password to access an authenticated proxy |

1. To extract the agent installation files, from an elevated command prompt run `MMASetup-<platform>.exe /c` and it will prompt you for the path to extract files to.  Alternatively, you can specify the path by passing the arguments `MMASetup-<platform>.exe /c /t:<Full Path>`.  
2. To silently install the agent and configure it to report to a workspace in Azure commercial cloud, from the folder you extracted the setup files to type: 
   
     ```dos
    setup.exe /qn NOAPM=1 ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_AZURE_CLOUD_TYPE=0 OPINSIGHTS_WORKSPACE_ID="<your workspace ID>" OPINSIGHTS_WORKSPACE_KEY="<your workspace key>" AcceptEndUserLicenseAgreement=1
    ```

   or to configure the agent to report to Azure US Government cloud, type: 

     ```dos
    setup.exe /qn NOAPM=1 ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_AZURE_CLOUD_TYPE=1 OPINSIGHTS_WORKSPACE_ID="<your workspace ID>" OPINSIGHTS_WORKSPACE_KEY="<your workspace key>" AcceptEndUserLicenseAgreement=1
    ```
    >[!NOTE]
    >The string values for the parameters *OPINSIGHTS_WORKSPACE_ID* and *OPINSIGHTS_WORKSPACE_KEY* need to be encapsulated in double-quotes to instruct Windows Installer to interprit as valid options for the package. 

## <a name="install-the-agent-using-dsc-in-azure-automation"></a>Install the agent using DSC in Azure Automation

You can use the following script example to install the agent using Azure Automation DSC.   If you do not have an Automation account, see [Get started with Azure Automation](/azure/automation/) to understand requirements and steps for creating an Automation account required before using Automation DSC.  If you are not familiar with Automation DSC, review [Getting started with Automation DSC](../../automation/automation-dsc-getting-started.md).

The following example installs the 64-bit agent, identified by the `URI` value. You can also use the 32-bit version by replacing the URI value. The URIs for both versions are:

- Windows 64-bit agent - https://go.microsoft.com/fwlink/?LinkId=828603
- Windows 32-bit agent - https://go.microsoft.com/fwlink/?LinkId=828604


>[!NOTE]
>This procedure and script example does not support upgrading the agent already deployed to a Windows computer.

The 32-bit and 64-bit versions of the agent package have different product codes and new versions released also have a unique value.  The product code is a GUID that is the principal identification of an application or product and is represented by the Windows Installer **ProductCode** property.  The `ProductId` value in the **MMAgent.ps1** script has to match the product code from the 32-bit or 64-bit agent installer package.

To retrieve the product code from the agent install package directly, you can use Orca.exe from the [Windows SDK Components for Windows Installer Developers](https://msdn.microsoft.com/library/windows/desktop/aa370834%28v=vs.85%29.aspx) that is a component of the Windows Software Development Kit or using PowerShell following an [example script](https://www.scconfigmgr.com/2014/08/22/how-to-get-msi-file-information-with-powershell/)  written by a Microsoft Valuable Professional (MVP).  For either approach, you first need to extract the **MOMagent.msi** file from the MMASetup installation package.  This is shown earlier in the first step under the section [Install the agent using the command line](#install-the-agent-using-the-command-line).  

1. Import the xPSDesiredStateConfiguration DSC Module from [https://www.powershellgallery.com/packages/xPSDesiredStateConfiguration](https://www.powershellgallery.com/packages/xPSDesiredStateConfiguration) into Azure Automation.  
2.  Create Azure Automation variable assets for *OPSINSIGHTS_WS_ID* and *OPSINSIGHTS_WS_KEY*. Set *OPSINSIGHTS_WS_ID* to your Log Analytics workspace ID and set *OPSINSIGHTS_WS_KEY* to the primary key of your workspace.
3.  Copy the script and save it as MMAgent.ps1.

    ```powershell
    Configuration MMAgent
    {
        $OIPackageLocalPath = "C:\Deploy\MMASetup-AMD64.exe"
        $OPSINSIGHTS_WS_ID = Get-AutomationVariable -Name "OPSINSIGHTS_WS_ID"
        $OPSINSIGHTS_WS_KEY = Get-AutomationVariable -Name "OPSINSIGHTS_WS_KEY"

        Import-DscResource -ModuleName xPSDesiredStateConfiguration
        Import-DscResource -ModuleName PSDesiredStateConfiguration

        Node OMSnode {
            Service OIService
            {
                Name = "HealthService"
                State = "Running"
                DependsOn = "[Package]OI"
            }

            xRemoteFile OIPackage {
                Uri = "https://go.microsoft.com/fwlink/?LinkId=828603"
                DestinationPath = $OIPackageLocalPath
            }

            Package OI {
                Ensure = "Present"
                Path  = $OIPackageLocalPath
                Name = "Microsoft Monitoring Agent"
                ProductId = "8A7F2C51-4C7D-4BFD-9014-91D11F24AAE2"
                Arguments = '/C:"setup.exe /qn NOAPM=1 ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_ID=' + $OPSINSIGHTS_WS_ID + ' OPINSIGHTS_WORKSPACE_KEY=' + $OPSINSIGHTS_WS_KEY + ' AcceptEndUserLicenseAgreement=1"'
                DependsOn = "[xRemoteFile]OIPackage"
            }
        }
    }

    ```

4. Update the `ProductId` value in the script with the product code extracted from the latest version of the agent install package using the methods recommended earlier. 
5. [Import the MMAgent.ps1 configuration script](../../automation/automation-dsc-getting-started.md#importing-a-configuration-into-azure-automation) into your Automation account. 
5. [Assign a Windows computer or node](../../automation/automation-dsc-getting-started.md#onboarding-an-azure-vm-for-management-with-azure-automation-state-configuration) to the configuration. Within 15 minutes, the node checks its configuration and the agent is pushed to the node.

## <a name="verify-agent-connectivity-to-log-analytics"></a>Verify agent connectivity to Log Analytics

Once installation of the agent is complete, verifying it is successfully connected and reporting can be accomplished in two ways.  

No computador, no **Painel de Controlo**, localize o item **Microsoft Monitoring Agent**.  Select it and on the **Azure Log Analytics** tab, the agent should display a message stating: **The Microsoft Monitoring Agent has successfully connected to the Microsoft Operations Management Suite service.**<br><br> ![Estado da ligação do MMA ao Log Analytics](media/agent-windows/log-analytics-mma-laworkspace-status.png)

You can also perform a simple log query in the Azure portal.  

1. No portal do Azure, clique em **Todos os serviços**. In the list of resources, type **Azure Monitor**. À medida que começa a escrever, a lista filtra com base na sua entrada. Select **Azure Monitor**.  
2. Select **Logs** in the menu. 
2. On the Logs pane, in the query field type:  

    ```
    Heartbeat 
    | where Category == "Direct Agent" 
    | where TimeGenerated > ago(30m)  
    ```

In the search results returned, you should see heartbeat records for the computer indicating it is connected and reporting to the service.   

## <a name="next-steps"></a>Passos seguintes

- Review [Managing and maintaining the Log Analytics agent for Windows and Linux](agent-manage.md) to learn about how to reconfigure, upgrade, or remove the agent from the virtual machine.

- Review [Troubleshooting the Windows agent](agent-windows-troubleshoot.md) if you encounter issues while installing or managing the agent.
