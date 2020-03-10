---
title: Ativar o Monitor Azure para VMs (clássico) com PowerShell ou modelos
description: Este artigo descreve como permite o Monitor Azure para VMs para uma ou mais máquinas virtuais Azure ou conjuntos de escala de máquinas virtuais utilizando modelos Azure PowerShell ou Azure Resource Manager.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/14/2019
ms.openlocfilehash: e28a5dce4dda677ef4e5eb0ed08c42ec1f03c308
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78395812"
---
# <a name="enable-azure-monitor-for-vms-preview-using-azure-powershell-or-resource-manager-templates"></a>Ativar o Monitor Azure para VMs (pré-visualização) utilizando modelos De PowerShell ou Gestor de Recursos Azure

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Este artigo explica como ativar o Monitor Azure para VMs (pré-visualização) para máquinas virtuais Azure ou conjuntos de escala de máquinas virtuais utilizando modelos Azure PowerShell ou Azure Resource Manager. No final deste processo, terá começado a monitorizar com sucesso todas as suas máquinas virtuais e a saber se alguma está a ter problemas de desempenho ou disponibilidade.

## <a name="set-up-a-log-analytics-workspace"></a>Configurar uma área de trabalho do Log Analytics

Se não tiver um espaço de trabalho de Log Analytics, tem de criar um. Reveja os métodos sugeridos na secção [Pré-Requisitos](vminsights-enable-overview.md#log-analytics) antes de continuar com os passos para configurá-lo. Em seguida, pode terminar a implantação do Monitor Azure para VMs utilizando o método do modelo do Gestor de Recursos Azure.

### <a name="enable-performance-counters"></a>Ativar os contadores de desempenho

Se a área de trabalho do Log Analytics que é referenciada pela solução já não está configurada para recolher os contadores de desempenho necessários para a solução, terá de ativá-las. Pode fazê-lo de duas maneiras:
* Manualmente, como descrito nas fontes de dados de [desempenho do Windows e do Linux no Log Analytics](../../azure-monitor/platform/data-sources-performance-counters.md)
* Ao descarregar e executar um script PowerShell que está disponível na [Galeria Azure PowerShell](https://www.powershellgallery.com/packages/Enable-VMInsightsPerfCounters/1.1)

### <a name="install-the-servicemap-solution"></a>Instalar a solução ServiceMap

Esse método inclui um modelo JSON que especifica a configuração para permitir que os componentes da solução na sua área de trabalho do Log Analytics.

Se não sabe como implementar recursos usando um modelo, consulte:
* [Implementar recursos com modelos do Resource Manager e o Azure PowerShell](../../azure-resource-manager/templates/deploy-powershell.md)
* [Implementar recursos com modelos de Gestor de Recursos e o Azure CLI](../../azure-resource-manager/templates/deploy-cli.md)

Para utilizar o Azure CLI, é necessário instalar e utilizar o CLI localmente. Tem de executar a CLI do Azure versão 2.0.27 ou posterior. Para identificar a sua versão, execute `az --version`. Para instalar ou atualizar o Azure CLI, consulte [Instalar o Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

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

1. Guarde este ficheiro como *instalaçãoforvminsights.json* para uma pasta local.

1. Capture os valores para *WorkspaceName*, *ResourceGroupName*, e *WorkspaceLocation*. O valor para *WorkspaceName* é o nome do seu espaço de trabalho Log Analytics. O valor para *workspaceLocation* é a região onde o espaço de trabalho está definido.

1. Está pronto para implementar este modelo.

    * Utilize os seguintes comandos do PowerShell na pasta que contém o modelo:

        ```powershell
        New-AzResourceGroupDeployment -Name DeploySolutions -TemplateFile InstallSolutionsForVMInsights.json -ResourceGroupName <ResourceGroupName> -WorkspaceName <WorkspaceName> -WorkspaceLocation <WorkspaceLocation - example: eastus>
        ```

        A mudança de configuração pode demorar alguns minutos a terminar. Quando está terminado, uma mensagem mostra que é semelhante ao seguinte e inclui o resultado:

        ```output
        provisioningState       : Succeeded
        ```

    * Para executar o seguinte comando com a CLI do Azure:

        ```azurecli
        az login
        az account set --subscription "Subscription Name"
        az group deployment create --name DeploySolutions --resource-group <ResourceGroupName> --template-file InstallSolutionsForVMInsights.json --parameters WorkspaceName=<workspaceName> WorkspaceLocation=<WorkspaceLocation - example: eastus>
        ```

        A mudança de configuração pode demorar alguns minutos a terminar. Quando está terminado, é apresentada uma mensagem semelhante à seguinte e inclui o resultado:

        ```output
        provisioningState       : Succeeded
        ```

## <a name="enable-with-azure-resource-manager-templates"></a>Ativar com modelos de Gestor de Recursos Azure

Criámos modelos de Gestor de Recursos Azure para embarcar nas suas máquinas virtuais e conjuntos de escala de máquinas virtuais. Estes modelos incluem cenários que pode utilizar para permitir a monitorização de um recurso existente e criar um novo recurso que tenha ativado a monitorização.

>[!NOTE]
>O modelo tem de ser implantado no mesmo grupo de recursos que o recurso a ser trazido a bordo.

Se não sabe como implementar recursos usando um modelo, consulte:
* [Implementar recursos com modelos do Resource Manager e o Azure PowerShell](../../azure-resource-manager/templates/deploy-powershell.md)
* [Implementar recursos com modelos de Gestor de Recursos e o Azure CLI](../../azure-resource-manager/templates/deploy-cli.md)

Para utilizar o Azure CLI, é necessário instalar e utilizar o CLI localmente. Tem de executar a CLI do Azure versão 2.0.27 ou posterior. Para identificar a sua versão, execute `az --version`. Para instalar ou atualizar o Azure CLI, consulte [Instalar o Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

### <a name="download-templates"></a>Modelos de descarregamento

Os modelos do Gestor de Recursos Azure são fornecidos num ficheiro de arquivo (.zip) que pode [descarregar](https://aka.ms/VmInsightsARMTemplates) a partir do nosso repo GitHub. O conteúdo do ficheiro inclui pastas que representam cada cenário de implementação com um modelo e ficheiro de parâmetro. Antes de executá-los, modifique o ficheiro de parâmetros e especifique os valores necessários. Não modifique o ficheiro do modelo a menos que precise personalizá-lo para suportar os seus requisitos específicos. Depois de ter modificado o ficheiro do parâmetro, pode implantá-lo utilizando os seguintes métodos descritos posteriormente neste artigo.

O ficheiro de descarregamento contém os seguintes modelos para diferentes cenários:

- O modelo **vmOnboarding existente** permite o Monitor Azure para VMs se a máquina virtual já existir.
- O modelo **NewVmOnboarding** cria uma máquina virtual e permite que o Monitor Azure para vMs a monitorize.
- O modelo **vmssOnboarding existente** permite o Monitor Azure para VMs se o conjunto de escala de máquina virtual já existir.
- O modelo **NewVmssOnboarding** cria conjuntos de escala de máquina virtual e permite que o Monitor Azure para os VMs os monitorize.
- O modelo **ConfigureWorkspace** configura o seu espaço de trabalho Log Analytics para suportar o Monitor Azure para VMs, permitindo as soluções e recolha de contadores de desempenho do sistema operativo Linux e Windows.

>[!NOTE]
>Se os conjuntos de escala de máquina virtual já estivessem presentes e a política de atualização for definida como **Manual**, o Monitor Azure para VMs não será ativado por padrão após executar o modelo **existente VmssOnboarding** Azure Resource Manager. Tem que atualizar manualmente as instâncias.

### <a name="deploy-by-using-azure-powershell"></a>Implementar com o Azure PowerShell

O passo seguinte permite a monitorização utilizando o Azure PowerShell.

```powershell
New-AzResourceGroupDeployment -Name OnboardCluster -ResourceGroupName <ResourceGroupName> -TemplateFile <Template.json> -TemplateParameterFile <Parameters.json>
```
A mudança de configuração pode demorar alguns minutos a terminar. Quando está terminado, uma mensagem mostra que é semelhante ao seguinte e inclui o resultado:

```output
provisioningState       : Succeeded
```

### <a name="deploy-by-using-the-azure-cli"></a>Implementar utilizando o Azure CLI

O passo seguinte permite a monitorização utilizando o CLI Azure.

```azurecli
az login
az account set --subscription "Subscription Name"
az group deployment create --resource-group <ResourceGroupName> --template-file <Template.json> --parameters <Parameters.json>
```

A saída assemelha-se ao seguinte:

```output
provisioningState       : Succeeded
```

## <a name="enable-with-powershell"></a>Ativar com o PowerShell

Para ativar o Monitor Azure para VMs para vários VMs ou conjuntos de escala de máquinas virtuais, utilize o script PowerShell [Install-VMInsights.ps1](https://www.powershellgallery.com/packages/Install-VMInsights/1.0). Está disponível na Galeria Azure PowerShell. Este guião iterates através de:

- Todas as máquinas virtuais e a escala de máquinas virtuais definidas na sua subscrição.
- O grupo de recursos com âmbito especificado pelo *ResourceGroup.*
- Um único conjunto de escala de VM ou máquina virtual especificado pelo *Nome*.

Para cada conjunto de dimensionamento VM ou numa máquina virtual, o script verifica se a extensão da VM já está instalada. Se a extensão VM estiver instalada, o script tenta reinstalá-la. Se a extensão VM não estiver instalada, o script instala as extensões VM do agente de Log Analytics e Dependência.

Verifique se está a utilizar o módulo Azure PowerShell Az versão 1.0.0 ou mais tarde com `Enable-AzureRM` pseudónimos de compatibilidade ativados. Executar `Get-Module -ListAvailable Az` para localizar a versão. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](https://docs.microsoft.com/powershell/azure/install-az-ps). Se estiver a executar a PowerShell localmente, também precisa de executar `Connect-AzAccount` para criar uma ligação com o Azure.

Para obter uma lista dos detalhes do argumento do script e uso de exemplo, faça `Get-Help`.

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

Agora que a monitorização está ativada para as suas máquinas virtuais, esta informação está disponível para análise com o Monitor Azure para VMs.

- Para visualizar as dependências de aplicações descobertas, consulte o View Azure Monitor para o Mapa de [VMs](vminsights-maps.md).

- Para identificar estrangulamentos e utilização geral com o desempenho do seu VM, consulte [o View Azure VM Performance](vminsights-performance.md).
