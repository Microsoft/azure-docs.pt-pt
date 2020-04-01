---
title: Utilize modelos do Gestor de Recursos Azure para a Gestão de Atualização a bordo [ Microsoft Docs
description: Pode utilizar um modelo de Gestor de Recursos Azure para embarcar na solução azure Automation Update Management.
ms.service: automation
ms.subservice: update-management
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 03/30/2020
ms.openlocfilehash: e69f3d7350d0da9f364983eae0935532b576bd76
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/31/2020
ms.locfileid: "80411464"
---
# <a name="onboard-update-management-solution-using-azure-resource-manager-template"></a>Solução de Gestão de Atualização de Bordo utilizando o modelo de Gestor de Recursos Azure

Pode utilizar modelos do Gestor de [Recursos Azure](../azure-resource-manager/templates/template-syntax.md) para ativar a solução de Gestão de Atualização de Automação Azure no seu grupo de recursos. Este artigo fornece um modelo de amostra que automatiza o seguinte:

* Criação de um espaço de trabalho Azure Monitor Log Analytics.
* Criação de uma conta Azure Automation.
* Liga a conta De automação ao espaço de trabalho do Log Analytics se ainda não estiver ligado.
* A bordo da solução azure Automation Update Management

O modelo não automatiza o embarque de um ou mais VMs Azure ou não-Azure.

Se já tem uma conta de log Analytics e de automação implantada numa região suportada na sua subscrição, não estão ligadas, e o espaço de trabalho ainda não tem a solução DeGestão de Atualização implementada, utilizando este modelo com sucesso cria o link e implementa a solução de Gestão de Atualização. 

## <a name="api-versions"></a>Versões da API

A tabela seguinte lista a versão API pelos recursos utilizados neste exemplo.

| Recurso | Tipo de recurso | Versão API |
|:---|:---|:---|
| Área de trabalho | áreas de trabalho | Antevisão 2017-03-15 |
| Conta de automatização | automation | 2015-10-31 | 
| Solução | soluções | Antevisão 2015-11-01 |

## <a name="before-using-the-template"></a>Antes de usar o modelo

Se optar por instalar e utilizar o PowerShell localmente, este artigo requer o módulo Azure PowerShell Az. Executar `Get-Module -ListAvailable Az` para localizar a versão. Se precisar de atualizar, veja [Instalar o módulo do Azure PowerShell](/powershell/azure/install-az-ps). Se estiver a executar localmente o PowerShell, também terá de executar o `Connect-AzAccount` para criar uma ligação com o Azure. Com o Azure PowerShell, a implementação utiliza a [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment).

Se optar por instalar e utilizar o CLI localmente, este artigo requer que esteja a executar a versão 2.1.0 do Azure CLI ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). Com o Azure CLI, esta implementação utiliza a criação de implementação do [grupo Az](https://docs.microsoft.com/cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-create). 

O modelo JSON está configurado para o solicitar:

* O nome do espaço de trabalho
* A região para criar o espaço de trabalho em
* O nome da conta Automation
* A região para criar a conta em

O modelo JSON especifica um valor predefinido para os outros parâmetros que provavelmente seriam usados como uma configuração padrão no seu ambiente. Pode armazenar o modelo numa conta de armazenamento Azure para acesso partilhado na sua organização. Para mais informações sobre o trabalho com modelos, consulte Implementar recursos com modelos de Gestor de [Recursos e ClI Azure](../azure-resource-manager/templates/deploy-cli.md).

Os seguintes parâmetros no modelo são definidos com um valor predefinido para o espaço de trabalho log Analytics:

* sku - incumprimentos ao novo nível de preços Per-GB lançado no modelo de preços de abril de 2018
* retenção de dados - incumprimentos a trinta dias
* reserva de capacidade - incumprimentos a 100 GB

>[!WARNING]
>Se criar ou configurar um espaço de trabalho log Analytics numa subscrição que tenha optado pelo novo modelo de preços de abril de 2018, o único nível de preços válido do Log Analytics é **o PerGB2018.**
>

>[!NOTE]
>Antes de utilizar este modelo, reveja [detalhes adicionais](../azure-monitor/platform/template-workspace-configuration.md#create-a-log-analytics-workspace) para entender totalmente as opções de configuração do espaço de trabalho, tais como o modo de controlo de acesso, o nível de preços, a retenção e o nível de reserva de capacidade. Se é novo em registos do Monitor Azure e ainda não implementou um espaço de trabalho, deverá rever a orientação de design do espaço de [trabalho](../azure-monitor/platform/design-logs-deployment.md) para aprender sobre o controlo de acesso, e uma compreensão das estratégias de implementação do design que recomendamos para a sua organização.

## <a name="deploy-template"></a>Implementar o modelo

1. Copie e cole a seguinte sintaxe JSON no seu ficheiro:

    ```json
    {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceName": {
            "type": "string",
            "metadata": {
                "description": "Workspace name"
            }
        },
        "sku": {
            "type": "string",
            "allowedValues": [
                "pergb2018",
                "Free",
                "Standalone",
                "PerNode",
                "Standard",
                "Premium"
            ],
            "defaultValue": "pergb2018",
            "metadata": {
                "description": "Pricing tier: perGB2018 or legacy tiers (Free, Standalone, PerNode, Standard or Premium) which are not available to all customers."
            }
        },
        "dataRetention": {
            "type": "int",
            "defaultValue": 30,
            "minValue": 7,
            "maxValue": 730,
            "metadata": {
                "description": "Number of days of retention. Workspaces in the legacy Free pricing tier can only have 7 days."
            }
        },
        "immediatePurgeDataOn30Days": {
            "type": "bool",
            "defaultValue": "[bool('false')]",
            "metadata": {
                "description": "If set to true when changing retention to 30 days, older data will be immediately deleted. Use this with extreme caution. This only applies when retention is being set to 30 days."
            }
        },
        "location": {
            "type": "string",
            "allowedValues": [
                "australiacentral",
                "australiaeast",
                "australiasoutheast",
                "brazilsouth",
                "canadacentral",
                "centralindia",
                "centralus",
                "eastasia",
                "eastus",
                "eastus2",
                "francecentral",
                "japaneast",
                "koreacentral",
                "northcentralus",
                "northeurope",
                "southafricanorth",
                "southcentralus",
                "southeastasia",
                "uksouth",
                "ukwest",
                "westcentralus",
                "westeurope",
                "westus",
                "westus2"
            ],
            "metadata": {
                "description": "Specifies the location in which to create the workspace."
            }
        },
        "automationAccountName": {
            "type": "string",
            "metadata": {
                "description": "Automation account name"
            }
        },
        "automationAccountLocation": {
            "type": "string",
            "metadata": {
                "description": "Specify the location in which to create the Automation account."
            }
        }
    },
    "variables": {
        "Updates": {
            "name": "[concat('Updates', '(', parameters('workspaceName'), ')')]",
            "galleryName": "Updates"
        }
    },
    "resources": [
        {
        "type": "Microsoft.OperationalInsights/workspaces",
            "name": "[parameters('workspaceName')]",
            "apiVersion": "2017-03-15-preview",
            "location": "[parameters('location')]",
            "properties": {
                "sku": {
                    "Name": "[parameters('sku')]",
                    "name": "CapacityReservation",
                    "capacityReservationLevel": 100
                },
                "retentionInDays": "[parameters('dataRetention')]",
                "features": {
                    "searchVersion": 1,
                    "legacy": 0,
                    "enableLogAccessUsingOnlyResourcePermissions": true
                }
            },
            "resources": [
                {
                    "apiVersion": "2015-11-01-preview",
                    "location": "[resourceGroup().location]",
                    "name": "[variables('Updates').name]",
                    "type": "Microsoft.OperationsManagement/solutions",
                    "id": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.OperationsManagement/solutions/', variables('Updates').name)]",
                    "dependsOn": [
                        "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
                    ],
                    "properties": {
                        "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
                    },
                    "plan": {
                        "name": "[variables('Updates').name]",
                        "publisher": "Microsoft",
                        "promotionCode": "",
                        "product": "[concat('OMSGallery/', variables('Updates').galleryName)]"
                    }
                }
            ]
        },
        {
            "type": "Microsoft.Automation/automationAccounts",
            "apiVersion": "2015-01-01-preview",
            "name": "[parameters('automationAccountName')]",
            "location": "[parameters('automationAccountLocation')]",
            "dependsOn": [],
            "tags": {},
            "properties": {
                "sku": {
                    "name": "Basic"
                }
            },
        },
        {
            "apiVersion": "2015-11-01-preview",
            "type": "Microsoft.OperationalInsights/workspaces/linkedServices",
            "name": "[concat(parameters('workspaceName'), '/' , 'Automation')]",
            "location": "[resourceGroup().location]",
            "dependsOn": [
                "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]",
                "[concat('Microsoft.Automation/automationAccounts/', parameters('automationAccountName'))]"
            ],
            "properties": {
                "resourceId": "[resourceId('Microsoft.Automation/automationAccounts/', parameters('automationAccountName'))]"
            }
        }
      ]
    }
    ```

2. Editar o modelo para satisfazer os seus requisitos. Considere criar um ficheiro de [parâmetros do Gestor](../azure-resource-manager/templates/parameter-files.md) de Recursos em vez de passar os parâmetros como valores inline.

3. Guarde este ficheiro como deployUMSolutiontemplate.json para uma pasta local.

4. Está pronto para implementar este modelo. Pode utilizar o PowerShell ou o Azure CLI. Quando for solicitado um espaço de trabalho e nome de conta Automation, forneça um nome globalmente único em todas as subscrições do Azure.

    **PowerShell**

    ```powershell
    New-AzResourceGroupDeployment -Name <deployment-name> -ResourceGroupName <resource-group-name> -TemplateFile deployUMSolutiontemplate.json
    ```

    **CLI do Azure**

    ```cli
    az group deployment create --resource-group <my-resource-group> --name <my-deployment-name> --template-file deployUMSolutiontemplate.json
    ```

    A implementação pode demorar alguns minutos a concluir. Quando termina, vê-se uma mensagem semelhante à seguinte que inclui o resultado:

    ![Resultado do exemplo quando a implementação está completa](media/automation-update-management-deploy-template/template-output.png)

## <a name="next-steps"></a>Passos seguintes

Agora que tem a solução de Gestão de Atualização implementada, pode ativar VMs para gestão, avaliações de atualização de revisão e implementação de atualizações para os colocar em conformidade.

- A partir do seu [Azure Automation conta](automation-onboard-solutions-from-automation-account.md) uma ou mais máquinas Azure e manualmente para máquinas não Azure.

- Para um único Azure VM a partir da página de máquina virtual no portal Azure. Este cenário está disponível para [VMs Linux](../virtual-machines/linux/tutorial-config-management.md#enable-update-management) e [Windows.](../virtual-machines/windows/tutorial-config-management.md#enable-update-management)

- Para [vários VMs Azure](manage-update-multi.md) selecionando-os a partir da página de **máquinas Virtuais** no portal Azure. 