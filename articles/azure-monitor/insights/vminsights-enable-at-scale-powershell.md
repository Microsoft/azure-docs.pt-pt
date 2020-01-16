---
title: Habilitar Azure Monitor para VMs (clássico) com o PowerShell ou modelos
description: Este artigo descreve como habilitar Azure Monitor para VMs para uma ou mais máquinas virtuais do Azure ou conjuntos de dimensionamento de máquinas virtuais usando modelos de Azure PowerShell ou Azure Resource Manager.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/14/2019
ms.openlocfilehash: 4fc5afe3bbb4b2ccf2329432347b23fe9a69c5ea
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/15/2020
ms.locfileid: "75977680"
---
# <a name="enable-azure-monitor-for-vms-preview-using-azure-powershell-or-resource-manager-templates"></a>Habilitar Azure Monitor para VMs (versão prévia) usando modelos de Azure PowerShell ou do Resource Manager

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Este artigo explica como habilitar a Azure Monitor para VMs (versão prévia) para máquinas virtuais do Azure ou conjuntos de dimensionamento de máquinas virtuais usando modelos de Azure PowerShell ou Azure Resource Manager. No final desse processo, você terá iniciado com êxito o monitoramento de todas as suas máquinas virtuais e saber se alguma delas está apresentando problemas de desempenho ou disponibilidade.

## <a name="set-up-a-log-analytics-workspace"></a>Configurar uma área de trabalho do Log Analytics

Se você não tiver um espaço de trabalho Log Analytics, precisará criar um. Examine os métodos sugeridos na seção [pré-requisitos](vminsights-enable-overview.md#log-analytics) antes de continuar com as etapas para configurá-lo. Em seguida, você pode concluir a implantação de Azure Monitor para VMs usando o método de modelo Azure Resource Manager.

### <a name="enable-performance-counters"></a>Ativar os contadores de desempenho

Se a área de trabalho do Log Analytics que é referenciada pela solução já não está configurada para recolher os contadores de desempenho necessários para a solução, terá de ativá-las. Você pode fazer isso de uma das duas maneiras:
* Manualmente, conforme descrito em [Windows e Linux origens de dados de desempenho do Log Analytics](../../azure-monitor/platform/data-sources-performance-counters.md)
* Baixando e executando um script do PowerShell que está disponível na [Galeria de Azure PowerShell](https://www.powershellgallery.com/packages/Enable-VMInsightsPerfCounters/1.1)

### <a name="install-the-servicemap-solution"></a>Instalar a solução ServiceMap

Esse método inclui um modelo JSON que especifica a configuração para permitir que os componentes da solução na sua área de trabalho do Log Analytics.

Se você não souber como implantar recursos usando um modelo, consulte:
* [Implementar recursos com modelos do Resource Manager e o Azure PowerShell](../../azure-resource-manager/templates/deploy-powershell.md)
* [Implementar recursos com modelos do Resource Manager e a CLI do Azure](../../azure-resource-manager/templates/deploy-cli.md)

Para usar o CLI do Azure, primeiro você precisa instalar e usar a CLI localmente. Tem de executar a CLI do Azure versão 2.0.27 ou posterior. Para identificar a versão, execute `az --version`. Para instalar ou atualizar o CLI do Azure, consulte [instalar o CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

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
                    }
                ]
            }
        ]
    }
    ```

1. Guarde este ficheiro como *installsolutionsforvminsights.json* para uma pasta local.

1. Capture os valores para *WorkspaceName*, *ResourceGroupName*e *WorkspaceLocation*. O valor de *WorkspaceName* é o nome do seu espaço de trabalho do log Analytics. O valor para *WorkspaceLocation* é a região a área de trabalho está definida.

1. Está pronto para implementar este modelo.

    * Utilize os seguintes comandos do PowerShell na pasta que contém o modelo:

        ```powershell
        New-AzResourceGroupDeployment -Name DeploySolutions -TemplateFile InstallSolutionsForVMInsights.json -ResourceGroupName <ResourceGroupName> -WorkspaceName <WorkspaceName> -WorkspaceLocation <WorkspaceLocation - example: eastus>
        ```

        A alteração de configuração pode levar alguns minutos para ser concluída. Quando ele for concluído, uma mensagem será exibida semelhante à seguinte e inclui o resultado:

        ```powershell
        provisioningState       : Succeeded
        ```

    * Para executar o seguinte comando com a CLI do Azure:

        ```azurecli
        az login
        az account set --subscription "Subscription Name"
        az group deployment create --name DeploySolutions --resource-group <ResourceGroupName> --template-file InstallSolutionsForVMInsights.json --parameters WorkspaceName=<workspaceName> WorkspaceLocation=<WorkspaceLocation - example: eastus>
        ```

        A alteração de configuração pode levar alguns minutos para ser concluída. Quando for concluído, será exibida uma mensagem semelhante à seguinte e inclui o resultado:

        ```azurecli
        provisioningState       : Succeeded
        ```

## <a name="enable-with-azure-resource-manager-templates"></a>Habilitar com modelos de Azure Resource Manager

Criamos exemplos de modelos de Azure Resource Manager para integração de suas máquinas virtuais e conjuntos de dimensionamento de máquinas virtuais. Esses modelos incluem cenários que você pode usar para habilitar o monitoramento em um recurso existente e criar um novo recurso com monitoramento habilitado.

>[!NOTE]
>O modelo precisa ser implantado no mesmo grupo de recursos que o recurso a ser colocado no quadro.

Se você não souber como implantar recursos usando um modelo, consulte:
* [Implementar recursos com modelos do Resource Manager e o Azure PowerShell](../../azure-resource-manager/templates/deploy-powershell.md)
* [Implementar recursos com modelos do Resource Manager e a CLI do Azure](../../azure-resource-manager/templates/deploy-cli.md)

Para usar o CLI do Azure, primeiro você precisa instalar e usar a CLI localmente. Tem de executar a CLI do Azure versão 2.0.27 ou posterior. Para identificar a versão, execute `az --version`. Para instalar ou atualizar o CLI do Azure, consulte [instalar o CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

### <a name="download-templates"></a>Baixar modelos

Os modelos de Azure Resource Manager são fornecidos em um arquivo morto (. zip) que você pode [baixar](https://aka.ms/VmInsightsARMTemplates) de nosso repositório github. O conteúdo do arquivo inclui pastas que representam cada cenário de implantação com um arquivo de modelo e parâmetro. Antes de executá-los, modifique o arquivo de parâmetros e especifique os valores necessários. Não modifique o arquivo de modelo, a menos que você precise personalizá-lo para dar suporte a seus requisitos específicos. Depois de modificar o arquivo de parâmetro, você pode implantá-lo usando os seguintes métodos descritos posteriormente neste artigo.

O arquivo de download contém os seguintes modelos para cenários diferentes:

- O modelo **ExistingVmOnboarding** habilita Azure monitor para VMs se a máquina virtual já existe.
- O modelo **NewVmOnboarding** cria uma máquina virtual e permite Azure monitor para VMs monitorá-la.
- O modelo **ExistingVmssOnboarding** habilita Azure monitor para VMs se o conjunto de dimensionamento de máquinas virtuais já existir.
- O modelo **NewVmssOnboarding** cria conjuntos de dimensionamento de máquinas virtuais e permite que Azure monitor para VMs monitorá-los.
- O modelo **ConfigureWorkspace** configura seu espaço de trabalho log Analytics para dar suporte a Azure monitor para VMs habilitando as soluções e a coleta de contadores de desempenho do sistema operacional Linux e Windows.

>[!NOTE]
>Se os conjuntos de dimensionamento de máquinas virtuais já estiverem presentes e a política de atualização estiver definida como **manual**, Azure monitor para VMs não será habilitada para instâncias por padrão depois de executar o modelo de Azure Resource Manager **ExistingVmssOnboarding** . Você precisa atualizar manualmente as instâncias.

### <a name="deploy-by-using-azure-powershell"></a>Implementar com o Azure PowerShell

A etapa a seguir habilita o monitoramento usando Azure PowerShell.

```powershell
New-AzResourceGroupDeployment -Name OnboardCluster -ResourceGroupName <ResourceGroupName> -TemplateFile <Template.json> -TemplateParameterFile <Parameters.json>
```
A alteração de configuração pode levar alguns minutos para ser concluída. Quando ele for concluído, uma mensagem será exibida semelhante à seguinte e inclui o resultado:

```powershell
provisioningState       : Succeeded
```

### <a name="deploy-by-using-the-azure-cli"></a>Implantar usando o CLI do Azure

A etapa a seguir habilita o monitoramento usando o CLI do Azure.

```azurecli
az login
az account set --subscription "Subscription Name"
az group deployment create --resource-group <ResourceGroupName> --template-file <Template.json> --parameters <Parameters.json>
```

A saída é semelhante ao seguinte:

```azurecli
provisioningState       : Succeeded
```

## <a name="enable-with-powershell"></a>Ativar com o PowerShell

Para habilitar Azure Monitor para VMs para várias VMs ou conjuntos de dimensionamento de máquinas virtuais, use o script do PowerShell [install-VMInsights. ps1](https://www.powershellgallery.com/packages/Install-VMInsights/1.0). Ele está disponível na Galeria de Azure PowerShell. Este script itera por meio de:

- Cada máquina virtual e conjunto de dimensionamento de máquinas virtuais em sua assinatura.
- O grupo de recursos com escopo especificado pelo *resourcegroup*.
- Um único conjunto de dimensionamento de máquinas virtuais ou VM que é especificado pelo *nome*.

Para cada conjunto de dimensionamento VM ou numa máquina virtual, o script verifica se a extensão da VM já está instalada. Se a extensão de VM estiver instalada, o script tentará reinstalá-la. Se a extensão da VM não estiver instalada, o script instalará as extensões de VM do Log Analytics e do agente de dependência.

Verifique se você está usando Azure PowerShell módulo AZ versão 1.0.0 ou posterior com `Enable-AzureRM` aliases de compatibilidade habilitados. Executar `Get-Module -ListAvailable Az` para localizar a versão. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](https://docs.microsoft.com/powershell/azure/install-az-ps). Se estiver executando o PowerShell localmente, terá também de executar `Connect-AzAccount` para criar uma ligação com o Azure.

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

## <a name="next-steps"></a>Passos seguintes

Agora que o monitoramento está habilitado para suas máquinas virtuais, essas informações estão disponíveis para análise com Azure Monitor para VMs.

- Para ver dependências de aplicações detetadas, consulte [vista do Azure Monitor para o mapa de VMs](vminsights-maps.md).

- Para identificar afunilamentos e a utilização geral com o desempenho da VM, consulte [Exibir o desempenho da VM do Azure](vminsights-performance.md).
