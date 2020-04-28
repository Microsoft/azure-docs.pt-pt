---
title: Utilize modelos do Gestor de Recursos Azure para criar a conta de Automação [ Microsoft Docs
description: Pode utilizar um modelo de Gestor de Recursos Azure para criar uma conta De Automação Azure.
ms.service: automation
ms.subservice: update-management
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 04/24/2020
ms.openlocfilehash: 431b89df0ce06736a2e76e58797ded65751bb404
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "82165829"
---
# <a name="create-automation-account-using-azure-resource-manager-template"></a>Criar conta de automação usando o modelo de Gestor de Recursos Azure

Pode utilizar modelos do Gestor de [Recursos Azure](../azure-resource-manager/templates/template-syntax.md) para criar uma conta Azure Automation no seu grupo de recursos. Este artigo fornece um modelo de amostra que automatiza o seguinte:

* Criação de um espaço de trabalho Azure Monitor Log Analytics.
* Criação de uma conta Azure Automation.
* Liga a conta De automação ao espaço de trabalho do Log Analytics.

O modelo não automatiza o embarque de um ou mais VMs Azure ou não-Azure, ou soluções. 

>[!NOTE]
>Criação da execução de automação Como conta não é suportada quando se utiliza um modelo de Gestor de Recursos Azure. Para criar uma Conta Executar Manualmente a partir do portal ou com powerShell, consulte [Gerir Executar Como conta](manage-runas-account.md).

## <a name="api-versions"></a>Versões da API

A tabela seguinte lista a versão API pelos recursos utilizados neste exemplo.

| Recurso | Tipo de recurso | Versão API |
|:---|:---|:---|
| Área de trabalho | áreas de trabalho | Antevisão 2017-03-15 |
| Conta de automatização | automation | 2015-10-31 | 

## <a name="before-using-the-template"></a>Antes de usar o modelo

Se optar por instalar e utilizar o PowerShell localmente, este artigo requer o módulo Azure PowerShell Az. Executar `Get-Module -ListAvailable Az` para localizar a versão. Se precisar de atualizar, veja [Instalar o módulo do Azure PowerShell](/powershell/azure/install-az-ps). Se estiver a executar localmente o PowerShell, também terá de executar o `Connect-AzAccount` para criar uma ligação com o Azure. Com o Azure PowerShell, a implementação utiliza a [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment).

Se optar por instalar e utilizar o CLI localmente, este artigo requer que esteja a executar a versão 2.1.0 do Azure CLI ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). Com o Azure CLI, esta implementação utiliza a criação de implementação do [grupo Az](https://docs.microsoft.com/cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-create). 

O modelo JSON está configurado para o solicitar:

* O nome do espaço de trabalho
* A região para criar o espaço de trabalho em
* O nome da conta Automation
* A região para criar a conta em

Os seguintes parâmetros no modelo são definidos com um valor predefinido para o espaço de trabalho log Analytics:

* sku - incumprimentos ao novo nível de preços Per-GB lançado no modelo de preços de abril de 2018
* retenção de dados - incumprimentos a trinta dias
* reserva de capacidade - incumprimentos a 100 GB

>[!WARNING]
>Se criar ou configurar um espaço de trabalho log Analytics numa subscrição que tenha optado pelo novo modelo de preços de abril de 2018, o único nível de preços válido do Log Analytics é **o PerGB2018.**
>

O modelo JSON especifica um valor predefinido para os outros parâmetros que provavelmente seriam usados como uma configuração padrão no seu ambiente. Pode armazenar o modelo numa conta de armazenamento Azure para acesso partilhado na sua organização. Para mais informações sobre o trabalho com modelos, consulte Implementar recursos com modelos de Gestor de [Recursos e ClI Azure](../azure-resource-manager/templates/deploy-cli.md).

É importante compreender os seguintes detalhes de configuração se for novo no Azure Automation e no Azure Monitor, de forma a evitar erros ao tentar criar, configurar e utilizar um espaço de trabalho log Analytics ligado à sua nova conta Automation.

* Reveja [detalhes adicionais](../azure-monitor/platform/template-workspace-configuration.md#create-a-log-analytics-workspace) para entender completamente as opções de configuração do espaço de trabalho, tais como o modo de controlo de acesso, o nível de preços, a retenção e o nível de reserva de capacidade.

* Como apenas certas regiões são suportadas para ligar um espaço de trabalho log Analytics e uma conta de Automação na sua subscrição, reveja os [mapeamentos do Espaço de Trabalho](how-to/region-mappings.md) para especificar as regiões suportadas inline ou num ficheiro de parâmetros.

* Se é novo em registos do Monitor Azure e ainda não implementou um espaço de trabalho, deve rever a orientação de design do espaço de [trabalho](../azure-monitor/platform/design-logs-deployment.md) para aprender sobre o controlo de acesso, e compreender as estratégias de implementação do design que recomendamos para a sua organização.

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
        },
        "sampleGraphicalRunbookName": {
                "type": "String",
                "defaultValue": "AzureAutomationTutorial"
            },
            "sampleGraphicalRunbookDescription": {
                "type": "String",
                "defaultValue": " An example runbook which gets all the ARM resources using the Run As Account (Service Principal)."
            },
            "sampleGraphicalRunbookContentUri": {
                "type": "String",
                "defaultValue": "https://eus2oaasibizamarketprod1.blob.core.windows.net/marketplace-runbooks/AzureAutomationTutorial.graphrunbook"
            },
            "samplePowerShellRunbookName": {
                "type": "String",
                "defaultValue": "AzureAutomationTutorialScript"
            },
            "samplePowerShellRunbookDescription": {
                "type": "String",
                "defaultValue": " An example runbook which gets all the ARM resources using the Run As Account (Service Principal)."
            },
            "samplePowerShellRunbookContentUri": {
                "type": "String",
                "defaultValue": "https://eus2oaasibizamarketprod1.blob.core.windows.net/marketplace-runbooks/AzureAutomationTutorial.ps1"
            },
            "samplePython2RunbookName": {
                "type": "String",
                "defaultValue": "AzureAutomationTutorialPython2"
            },
            "samplePython2RunbookDescription": {
                "type": "String",
                "defaultValue": " An example runbook which gets all the ARM resources using the Run As Account (Service Principal)."
            },
            "samplePython2RunbookContentUri": {
                "type": "String",
                "defaultValue": "https://eus2oaasibizamarketprod1.blob.core.windows.net/marketplace-runbooks/AzureAutomationTutorialPython2.py"
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
            "type": "Microsoft.Automation/automationAccounts",
            "apiVersion": "2015-01-01-preview",
            "name": "[parameters('automationAccountName')]",
            "location": "[parameters('automationAccountLocation')]",
            "dependsOn": [
                 "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]",
            ],
            "tags": {},
            "properties": {
                "sku": {
                    "name": "Basic"
                }
            },
            "resources": [
                    {
                        "type": "runbooks",
                        "apiVersion": "2015-01-01-preview",
                        "name": "[parameters('sampleGraphicalRunbookName')]",
                        "location": "[parameters('automationAccountLocation')]",
                        "dependsOn": [
                            "[concat('Microsoft.Automation/automationAccounts/', parameters('automationAccountName'))]"
                        ],
                        "tags": {},
                        "properties": {
                            "runbookType": "GraphPowerShell",
                            "logProgress": "false",
                            "logVerbose": "false",
                            "description": "[parameters('sampleGraphicalRunbookDescription')]",
                            "publishContentLink": {
                                "uri": "[parameters('sampleGraphicalRunbookContentUri')]",
                                "version": "1.0.0.0"
                            }
                        }
                    },
                    {
                        "type": "runbooks",
                        "apiVersion": "2015-01-01-preview",
                        "name": "[parameters('samplePowerShellRunbookName')]",
                        "location": "[parameters('automationAccountLocation')]",
                        "dependsOn": [
                            "[concat('Microsoft.Automation/automationAccounts/', parameters('automationAccountName'))]"
                        ],
                        "tags": {},
                        "properties": {
                            "runbookType": "PowerShell",
                            "logProgress": "false",
                            "logVerbose": "false",
                            "description": "[parameters('samplePowerShellRunbookDescription')]",
                            "publishContentLink": {
                                "uri": "[parameters('samplePowerShellRunbookContentUri')]",
                                "version": "1.0.0.0"
                            }
                        }
                    },
                    {
                        "type": "runbooks",
                        "apiVersion": "2015-01-01-preview",
                        "name": "[parameters('samplePython2RunbookName')]",
                        "location": "[parameters('automationAccountLocation')]",
                        "dependsOn": [
                            "[concat('Microsoft.Automation/automationAccounts/', parameters('automationAccountName'))]"
                        ],
                        "tags": {},
                        "properties": {
                            "runbookType": "Python2",
                            "logProgress": "false",
                            "logVerbose": "false",
                            "description": "[parameters('samplePython2RunbookDescription')]",
                            "publishContentLink": {
                                "uri": "[parameters('samplePython2RunbookContentUri')]",
                                "version": "1.0.0.0"
                            }
                        }
                    }
                ]
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
     ]
    }
    ```

2. Editar o modelo para satisfazer os seus requisitos. Considere criar um ficheiro de [parâmetros do Gestor](../azure-resource-manager/templates/parameter-files.md) de Recursos em vez de passar os parâmetros como valores inline.

3. Guarde este ficheiro como deployAzAutomationAccttemplate.json para uma pasta local.

4. Está pronto para implementar este modelo. Pode utilizar o PowerShell ou o Azure CLI. Quando for solicitado um espaço de trabalho e nome de conta Automation, forneça um nome globalmente único em todas as subscrições do Azure.

    **PowerShell**

    ```powershell
    New-AzResourceGroupDeployment -Name <deployment-name> -ResourceGroupName <resource-group-name> -TemplateFile deployAzAutomationAccttemplate.json
    ```

    **CLI do Azure**

    ```cli
    az group deployment create --resource-group <my-resource-group> --name <my-deployment-name> --template-file deployAzAutomationAccttemplate.json
    ```

    A implementação pode demorar alguns minutos a concluir. Quando termina, vê-se uma mensagem semelhante à seguinte que inclui o resultado:

    ![Resultado do exemplo quando a implementação está completa](media/automation-create-account-template/template-output.png)

## <a name="next-steps"></a>Passos seguintes

Agora que tem uma conta de Automação, pode criar livros de execução e automatizar processos manuais.
