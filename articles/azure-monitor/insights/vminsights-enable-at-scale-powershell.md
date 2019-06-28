---
title: Ativar o Azure Monitor para VMs (pré-visualização) com modelos do Azure PowerShell ou do Resource Manager | Documentos da Microsoft
description: Este artigo descreve como ativar Azure Monitor para as VMs para um ou mais máquinas virtuais ou de dimensionamento de máquinas virtuais, define ao utilizar o Azure PowerShell ou os modelos Azure Resource Manager.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/09/2019
ms.author: magoedte
ms.openlocfilehash: ff284ea0adf6021ace84cd6a41f0a0e4e987a9c8
ms.sourcegitcommit: 22c97298aa0e8bd848ff949f2886c8ad538c1473
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/14/2019
ms.locfileid: "67144237"
---
# <a name="enable-azure-monitor-for-vms-preview-using-azure-powershell-or-resource-manager-templates"></a>Ativar o Azure Monitor para VMs (pré-visualização) com modelos do Azure PowerShell ou do Resource Manager

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Este artigo explica como ativar o Azure Monitor para VMs (pré-visualização) para máquinas virtuais do Azure ou conjuntos de dimensionamento de máquina virtual utilizando os modelos do Azure PowerShell ou do Azure Resource Manager. No final deste processo, será com êxito começaram a monitorização de todas as suas máquinas virtuais e saiba se ocorrerem problemas de disponibilidade de desempenho ou que qualquer um.

## <a name="set-up-a-log-analytics-workspace"></a>Configurar uma área de trabalho do Log Analytics 

Se não tiver uma área de trabalho do Log Analytics, terá de criar uma. Reveja os métodos que são sugeridos na [pré-requisitos](vminsights-enable-overview.md#log-analytics) secção antes de continuar com os passos para configurá-lo. Em seguida, pode concluir a implementação do Azure Monitor para VMs ao utilizar o método de modelo do Azure Resource Manager.

### <a name="enable-performance-counters"></a>Ativar os contadores de desempenho

Se a área de trabalho do Log Analytics que é referenciada pela solução já não está configurada para recolher os contadores de desempenho necessários para a solução, terá de ativá-las. Pode fazê-lo em uma das seguintes formas:
* Manualmente, conforme descrito em [Windows e Linux origens de dados de desempenho do Log Analytics](../../azure-monitor/platform/data-sources-performance-counters.md)
* Ao transferir e executar um script do PowerShell que está disponível a partir do [galeria do PowerShell do Azure](https://www.powershellgallery.com/packages/Enable-VMInsightsPerfCounters/1.1)

### <a name="install-the-servicemap-and-infrastructureinsights-solutions"></a>Instalar as soluções ServiceMap e InfrastructureInsights
Esse método inclui um modelo JSON que especifica a configuração para permitir que os componentes da solução na sua área de trabalho do Log Analytics.

Se não sabe como implementar recursos com um modelo, consulte:
* [Implementar recursos com modelos do Resource Manager e o Azure PowerShell](../../azure-resource-manager/resource-group-template-deploy.md)
* [Implementar recursos com modelos do Resource Manager e a CLI do Azure](../../azure-resource-manager/resource-group-template-deploy-cli.md)

Para utilizar a CLI do Azure, tem primeiro de instalar e utilizar a CLI localmente. Tem de executar a CLI do Azure versão 2.0.27 ou posterior. Para identificar a versão, execute `az --version`. Para instalar ou atualizar a CLI do Azure, veja [instalar a CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

1. Copie e cole a seguinte sintaxe JSON no seu ficheiro:

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "WorkspaceName": {
                "type": "string"
            },
            "WorkspaceLocation": {
                "type": "string"
            }
        },
        "resources": [
            {
                "apiVersion": "2017-03-15-preview",
                "type": "Microsoft.OperationalInsights/workspaces",
                "name": "[parameters('WorkspaceName')]",
                "location": "[parameters('WorkspaceLocation')]",
                "resources": [
                    {
                        "apiVersion": "2015-11-01-preview",
                        "location": "[parameters('WorkspaceLocation')]",
                        "name": "[concat('ServiceMap', '(', parameters('WorkspaceName'),')')]",
                        "type": "Microsoft.OperationsManagement/solutions",
                        "dependsOn": [
                            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                        ],
                        "properties": {
                            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                        },

                        "plan": {
                            "name": "[concat('ServiceMap', '(', parameters('WorkspaceName'),')')]",
                            "publisher": "Microsoft",
                            "product": "[Concat('OMSGallery/', 'ServiceMap')]",
                            "promotionCode": ""
                        }
                    },
                    {
                        "apiVersion": "2015-11-01-preview",
                        "location": "[parameters('WorkspaceLocation')]",
                        "name": "[concat('InfrastructureInsights', '(', parameters('WorkspaceName'),')')]",
                        "type": "Microsoft.OperationsManagement/solutions",
                        "dependsOn": [
                            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                        ],
                        "properties": {
                            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                        },
                        "plan": {
                            "name": "[concat('InfrastructureInsights', '(', parameters('WorkspaceName'),')')]",
                            "publisher": "Microsoft",
                            "product": "[Concat('OMSGallery/', 'InfrastructureInsights')]",
                            "promotionCode": ""
                        }
                    }
                ]
            }
        ]
    }
    ```

1. Guarde este ficheiro como *installsolutionsforvminsights.json* para uma pasta local.

1. Capturar os valores para *WorkspaceName*, *ResourceGroupName*, e *WorkspaceLocation*. O valor para *WorkspaceName* é o nome da sua área de trabalho do Log Analytics. O valor para *WorkspaceLocation* é a região a área de trabalho está definida.

1. Está pronto para implementar este modelo.
 
    * Utilize os seguintes comandos do PowerShell na pasta que contém o modelo:

        ```powershell
        New-AzResourceGroupDeployment -Name DeploySolutions -TemplateFile InstallSolutionsForVMInsights.json -ResourceGroupName <ResourceGroupName> -WorkspaceName <WorkspaceName> -WorkspaceLocation <WorkspaceLocation - example: eastus>
        ```

        A alteração de configuração pode demorar alguns minutos a concluir. Quando estiver concluído, uma mensagem indica que é semelhante ao seguinte e inclui o resultado:

        ```powershell
        provisioningState       : Succeeded
        ```

    * Para executar o seguinte comando com a CLI do Azure:
    
        ```azurecli
        az login
        az account set --subscription "Subscription Name"
        az group deployment create --name DeploySolutions --resource-group <ResourceGroupName> --template-file InstallSolutionsForVMInsights.json --parameters WorkspaceName=<workspaceName> WorkspaceLocation=<WorkspaceLocation - example: eastus>
        ```

        A alteração de configuração pode demorar alguns minutos a concluir. Quando estiver concluído, será apresentada uma mensagem que é semelhante ao seguinte e inclui o resultado:

        ```azurecli
        provisioningState       : Succeeded
        ```

## <a name="enable-with-azure-resource-manager-templates"></a>Ativar com modelos Azure Resource Manager
Criamos modelos do Azure Resource Manager de exemplo para a integração suas máquinas virtuais e conjuntos de dimensionamento de máquina virtual. Estes modelos incluem cenários que pode usar para ativar a monitorização de um recurso existente e criar um novo recurso que tem a monitorização ativada.

>[!NOTE]
>O modelo precisa ser implantado no mesmo grupo de recursos que o recurso a ser colocados na organização.

Se não sabe como implementar recursos com um modelo, consulte:
* [Implementar recursos com modelos do Resource Manager e o Azure PowerShell](../../azure-resource-manager/resource-group-template-deploy.md)
* [Implementar recursos com modelos do Resource Manager e a CLI do Azure](../../azure-resource-manager/resource-group-template-deploy-cli.md)

Para utilizar a CLI do Azure, tem primeiro de instalar e utilizar a CLI localmente. Tem de executar a CLI do Azure versão 2.0.27 ou posterior. Para identificar a versão, execute `az --version`. Para instalar ou atualizar a CLI do Azure, veja [instalar a CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

### <a name="download-templates"></a>Transferir modelos

Os modelos Azure Resource Manager são fornecidos num ficheiro de arquivo (. zip) que pode [transferir](https://aka.ms/VmInsightsARMTemplates) do nosso repositório do GitHub. Conteúdo do arquivo incluem pastas que representam cada cenário de implantação com um ficheiro de modelo e o parâmetro. Antes de executá-los, modifique o ficheiro de parâmetros e especifique os valores necessários. Não modifique o arquivo de modelo, a menos que precise personalizá-lo para oferecer suporte a seus requisitos específicos. Depois de modificar o ficheiro de parâmetros, pode implementá-la através dos seguintes métodos descritos neste artigo. 

O arquivo de download contém os seguintes modelos para diferentes cenários:

- **ExistingVmOnboarding** modelo ativa do Azure Monitor para as VMs, se a máquina virtual já existe.
- **NewVmOnboarding** modelo cria uma máquina virtual e ativa o Azure Monitor para as VMs para monitorizá-lo.
- **ExistingVmssOnboarding** modelo ativa do Azure Monitor para as VMs, se o conjunto de dimensionamento já existe.
- **NewVmssOnboarding** modelo cria conjuntos de dimensionamento de máquinas virtuais e ativa o Azure Monitor para as VMs para monitorizá-las.
- **ConfigureWorksapce** modelo configura a sua área de trabalho do Log Analytics para suportar o Azure Monitor para VMs, permitindo que as soluções e uma coleção de contadores de desempenho do sistema operativo Linux e Windows.

>[!NOTE]
>Se os conjuntos de dimensionamento de máquina virtual já estavam presentes e a política de atualização está definida como **Manual**, do Azure Monitor para VMs não estar ativado para instâncias por predefinição depois de ser executada a **ExistingVmssOnboarding** Modelo do Azure Resource Manager. Terá de atualizar manualmente as instâncias.

### <a name="deploy-by-using-azure-powershell"></a>Implementar com o Azure PowerShell

O passo seguinte ativa a monitorização com o Azure PowerShell.

```powershell
New-AzResourceGroupDeployment -Name OnboardCluster -ResourceGroupName <ResourceGroupName> -TemplateFile <Template.json> -TemplateParameterFile <Parameters.json>
```
A alteração de configuração pode demorar alguns minutos a concluir. Quando estiver concluído, uma mensagem indica que é semelhante ao seguinte e inclui o resultado:

```powershell
provisioningState       : Succeeded
```
### <a name="deploy-by-using-the-azure-cli"></a>Implementar com a CLI do Azure

O passo seguinte ativa a monitorização utilizando a CLI do Azure.

```azurecli
az login
az account set --subscription "Subscription Name"
az group deployment create --resource-group <ResourceGroupName> --template-file <Template.json> --parameters <Parameters.json>
```

O resultado é semelhante ao seguinte:

```azurecli
provisioningState       : Succeeded
```

## <a name="enable-with-powershell"></a>Ativar com o PowerShell

Para ativar o Azure Monitor para as VMs para várias VMs ou conjuntos de dimensionamento de máquina virtual, utilize o script do PowerShell [Install-VMInsights.ps1](https://www.powershellgallery.com/packages/Install-VMInsights/1.0). Ele está disponível a partir da galeria do PowerShell do Azure. Este script faz a iteração por meio de:

- Cada máquina virtual e o dimensionamento de máquinas virtuais definido na sua subscrição.
- O grupo de recursos de âmbito especificado pelo *ResourceGroup*. 
- Um conjunto de dimensionamento VM ou numa máquina virtual único que é especificado por *nome*.

Para cada conjunto de dimensionamento VM ou numa máquina virtual, o script verifica se a extensão da VM já está instalada. Se a extensão de VM não estiver instalada, o script tenta reinstalá-lo. Se a extensão da VM estiver instalada, o script instala as extensões VM de agente do Log Analytics e de dependência.

Este script requer o módulo Az versão 1.0.0 do Azure PowerShell ou posterior. Executar `Get-Module -ListAvailable Az` para localizar a versão. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](https://docs.microsoft.com/powershell/azure/install-az-ps). Se estiver executando o PowerShell localmente, terá também de executar `Connect-AzAccount` para criar uma ligação com o Azure.

Para obter uma lista de detalhes do argumento e a utilização de exemplo do script, execute `Get-Help`.

```powershell
Get-Help .\Install-VMInsights.ps1 -Detailed

SYNOPSIS
    This script installs VM extensions for Log Analytics and the Dependency agent as needed for VM Insights.


SYNTAX
    .\Install-VMInsights.ps1 [-WorkspaceId] <String> [-WorkspaceKey] <String> [-SubscriptionId] <String> [[-ResourceGroup]
    <String>] [[-Name] <String>] [[-PolicyAssignmentName] <String>] [-ReInstall] [-TriggerVmssManualVMUpdate] [-Approve] [-WorkspaceRegion] <String>
    [-WhatIf] [-Confirm] [<CommonParameters>]


DESCRIPTION
    This script installs or reconfigures the following on VMs and virtual machine scale sets:
    - Log Analytics VM extension configured to supplied Log Analytics workspace
    - Dependency agent VM extension

    Can be applied to:
    - Subscription
    - Resource group in a subscription
    - Specific VM or virtual machine scale set
    - Compliance results of a policy for a VM or VM extension

    Script will show you a list of VMs or virtual machine scale sets that will apply to and let you confirm to continue.
    Use -Approve switch to run without prompting, if all required parameters are provided.

    If the extensions are already installed, they will not install again.
    Use -ReInstall switch if you need to, for example, update the workspace.

    Use -WhatIf if you want to see what would happen in terms of installs, what workspace configured to, and status of the extension.


PARAMETERS
    -WorkspaceId <String>
        Log Analytics WorkspaceID (GUID) for the data to be sent to

    -WorkspaceKey <String>
        Log Analytics Workspace primary or secondary key

    -SubscriptionId <String>
        SubscriptionId for the VMs/VM Scale Sets
        If using PolicyAssignmentName parameter, subscription that VMs are in

    -ResourceGroup <String>
        <Optional> Resource Group to which the VMs or VM Scale Sets belong

    -Name <String>
        <Optional> To install to a single VM/VM Scale Set

    -PolicyAssignmentName <String>
        <Optional> Take the input VMs to operate on as the Compliance results from this Assignment
        If specified will only take from this source.

    -ReInstall [<SwitchParameter>]
        <Optional> If VM/VM Scale Set is already configured for a different workspace, set this to change to the new workspace

    -TriggerVmssManualVMUpdate [<SwitchParameter>]
        <Optional> Set this flag to trigger update of VM instances in a scale set whose upgrade policy is set to Manual

    -Approve [<SwitchParameter>]
        <Optional> Gives the approval for the installation to start with no confirmation prompt for the listed VMs/VM Scale Sets

    -WorkspaceRegion <String>
        Region the Log Analytics Workspace is in
        Supported values: "East US","eastus","Southeast Asia","southeastasia","West Central US","westcentralus","West Europe","westeurope"
        For Health supported is: "East US","eastus","West Central US","westcentralus"

    -WhatIf [<SwitchParameter>]
        <Optional> See what would happen in terms of installs.
        If extension is already installed will show what workspace is currently configured, and status of the VM extension

    -Confirm [<SwitchParameter>]
        <Optional> Confirm every action

    <CommonParameters>
        This cmdlet supports the common parameters: Verbose, Debug,
        ErrorAction, ErrorVariable, WarningAction, WarningVariable,
        OutBuffer, PipelineVariable, and OutVariable. For more information, see
        about_CommonParameters (https:/go.microsoft.com/fwlink/?LinkID=113216).

    -------------------------- EXAMPLE 1 --------------------------
    .\Install-VMInsights.ps1 -WorkspaceRegion eastus -WorkspaceId <WorkspaceId>-WorkspaceKey <WorkspaceKey> -SubscriptionId <SubscriptionId>
    -ResourceGroup <ResourceGroup>

    Install for all VMs in a resource group in a subscription

    -------------------------- EXAMPLE 2 --------------------------
    .\Install-VMInsights.ps1 -WorkspaceRegion eastus -WorkspaceId <WorkspaceId>-WorkspaceKey <WorkspaceKey> -SubscriptionId <SubscriptionId>
    -ResourceGroup <ResourceGroup> -ReInstall

    Specify to reinstall extensions even if already installed, for example, to update to a different workspace

    -------------------------- EXAMPLE 3 --------------------------
    .\Install-VMInsights.ps1 -WorkspaceRegion eastus -WorkspaceId <WorkspaceId>-WorkspaceKey <WorkspaceKey> -SubscriptionId <SubscriptionId>
    -PolicyAssignmentName a4f79f8ce891455198c08736 -ReInstall

    Specify to use a PolicyAssignmentName for source and to reinstall (move to a new workspace)
```

O exemplo seguinte demonstra como utilizar os comandos do PowerShell na pasta para ativar o Azure Monitor para VMs e compreender a saída esperada:

```powershell
$WorkspaceId = "<GUID>"
$WorkspaceKey = "<Key>"
$SubscriptionId = "<GUID>"
.\Install-VMInsights.ps1 -WorkspaceId $WorkspaceId -WorkspaceKey $WorkspaceKey -SubscriptionId $SubscriptionId -WorkspaceRegion eastus

Getting list of VMs or virtual machine scale sets matching criteria specified

VMs or virtual machine scale sets matching criteria:

db-ws-1 VM running
db-ws2012 VM running

This operation will install the Log Analytics and Dependency agent extensions on the previous two VMs or virtual machine scale sets.
VMs in a non-running state will be skipped.
Extension will not be reinstalled if already installed. Use -ReInstall if desired, for example, to update workspace.

Confirm
Continue?
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): y

db-ws-1 : Deploying DependencyAgentWindows with name DAExtension
db-ws-1 : Successfully deployed DependencyAgentWindows
db-ws-1 : Deploying MicrosoftMonitoringAgent with name MMAExtension
db-ws-1 : Successfully deployed MicrosoftMonitoringAgent
db-ws2012 : Deploying DependencyAgentWindows with name DAExtension
db-ws2012 : Successfully deployed DependencyAgentWindows
db-ws2012 : Deploying MicrosoftMonitoringAgent with name MMAExtension
db-ws2012 : Successfully deployed MicrosoftMonitoringAgent

Summary:

Already onboarded: (0)

Succeeded: (4)
db-ws-1 : Successfully deployed DependencyAgentWindows
db-ws-1 : Successfully deployed MicrosoftMonitoringAgent
db-ws2012 : Successfully deployed DependencyAgentWindows
db-ws2012 : Successfully deployed MicrosoftMonitoringAgent

Connected to different workspace: (0)

Not running - start VM to configure: (0)

Failed: (0)
```

## <a name="next-steps"></a>Passos Seguintes

Agora que a monitorização estiver ativada para as suas máquinas virtuais, estas informações são disponíveis para análise com o Azure Monitor para as VMs.
 
- Para saber como utilizar a funcionalidade de estado de funcionamento, veja [vista do Azure Monitor de estado de funcionamento de VMs](vminsights-health.md). 
- Para ver dependências de aplicações detetadas, consulte [vista do Azure Monitor para o mapa de VMs](vminsights-maps.md). 
- Para identificar afunilamentos e a utilização geral com o desempenho da sua VM, consulte [vista de desempenho da VM do Azure](vminsights-performance.md). 
- Para ver dependências de aplicações detetadas, consulte [vista do Azure Monitor para o mapa de VMs](vminsights-maps.md).