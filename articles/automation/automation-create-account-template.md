---
title: Utilize modelos do Gestor de Recursos Azure para criar uma conta de Automação [ Microsoft Docs
description: Pode utilizar um modelo de Gestor de Recursos Azure para criar uma conta De Automação Azure.
ms.service: automation
ms.subservice: update-management
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 04/24/2020
ms.openlocfilehash: 949b07a16b2c2b08891d721e46948481cfe572b2
ms.sourcegitcommit: 309a9d26f94ab775673fd4c9a0ffc6caa571f598
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/09/2020
ms.locfileid: "82996107"
---
# <a name="create-an-automation-account-by-using-an-azure-resource-manager-template"></a>Criar uma conta de Automação usando um modelo de Gestor de Recursos Azure

Pode utilizar modelos do Gestor de [Recursos Azure](../azure-resource-manager/templates/template-syntax.md) para criar uma conta Azure Automation no seu grupo de recursos. Este artigo fornece um modelo de amostra que:

* Automatiza a criação de um espaço de trabalho Azure Monitor Log Analytics.
* Automatiza a criação de uma conta Azure Automation.
* Liga a conta De automação ao espaço de trabalho do Log Analytics.

O modelo não automatiza a habilitação de máquinas virtuais Azure ou não Azure. 

>[!NOTE]
>Criação da Execução de Automação Como conta não é suportada quando você está usando um modelo de Gestor de Recursos Azure. Para criar uma Conta Executar Manualmente a partir do portal ou com powerShell, consulte [Gerir Como contas](manage-runas-account.md).

## <a name="api-versions"></a>Versões da API

A tabela seguinte lista a versão API pelos recursos utilizados neste exemplo.

| Recurso | Tipo de recurso | Versão API |
|:---|:---|:---|
| Área de trabalho | áreas de trabalho | Antevisão 2017-03-15 |
| Conta de automatização | automation | 2015-10-31 | 

## <a name="before-you-use-the-template"></a>Antes de usar o modelo

Se optar por instalar e utilizar o PowerShell localmente, este artigo requer o módulo Azure PowerShell Az. Executar `Get-Module -ListAvailable Az` para localizar a versão. Se precisar de atualizar, veja [Instalar o módulo do Azure PowerShell](/powershell/azure/install-az-ps). Se estiver a executar a PowerShell localmente, também precisa de correr `Connect-AzAccount` para criar uma ligação com o Azure. Com o PowerShell, a implementação utiliza a [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment).

Se optar por instalar e utilizar o Azure CLI localmente, este artigo requer que esteja a executar a versão 2.1.0 ou mais tarde. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). Com o Azure CLI, esta implementação utiliza a criação de implementação do [grupo Az](https://docs.microsoft.com/cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-create). 

O modelo JSON está configurado para o solicitar:

* O nome do espaço de trabalho.
* A região para criar o espaço de trabalho.
* O nome da conta de Automação.
* A região para criar a conta.

Os seguintes parâmetros no modelo são definidos com um valor predefinido para o espaço de trabalho log Analytics:

* *sku* defaults para o nível de preços por GB lançado no modelo de preços de abril de 2018.
* *dadosA não paração* da retenção é de 30 dias.
* *capacidadeReservasN* de incumprimento saem a 100 GB.

>[!WARNING]
>Se quiser criar ou configurar um espaço de trabalho log Analytics numa subscrição que tenha optado pelo modelo de preços de abril de 2018, o único nível de preços válido do Log Analytics é *o PerGB2018.*
>

O modelo JSON especifica um valor predefinido para os outros parâmetros que provavelmente seriam usados como uma configuração padrão no seu ambiente. Pode armazenar o modelo numa conta de armazenamento Azure para acesso partilhado na sua organização. Para obter mais informações sobre o trabalho com modelos, consulte Implementar recursos com modelos de Gestor de [Recursos e o Azure CLI](../azure-resource-manager/templates/deploy-cli.md).

Se é novo no Azure Automation e no Azure Monitor, é importante que compreenda os seguintes detalhes de configuração. Podem ajudá-lo a evitar erros quando tenta criar, configurar e utilizar um espaço de trabalho log Analytics ligado à sua nova conta Desmótica. 

* Reveja [detalhes adicionais](../azure-monitor/platform/template-workspace-configuration.md#create-a-log-analytics-workspace) para entender completamente as opções de configuração do espaço de trabalho, tais como o modo de controlo de acesso, o nível de preços, a retenção e o nível de reserva de capacidade.

* Reveja os [mapeamentos do espaço](how-to/region-mappings.md) de trabalho para especificar as regiões suportadas inline ou num ficheiro de parâmetros. Apenas certas regiões são suportadas para ligar um espaço de trabalho log Analytics e uma conta de Automação na sua subscrição.

* Se é novo em registos do Monitor Azure e ainda não implementou um espaço de trabalho, deverá rever a [orientação](../azure-monitor/platform/design-logs-deployment.md)de design do espaço de trabalho . Irá ajudá-lo a aprender sobre o controlo de acesso, e entender as estratégias de implementação de design que recomendamos para a sua organização.

## <a name="deploy-the-template"></a>Implementar o modelo

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
                "description": "Pricing tier: perGB2018 or legacy tiers (Free, Standalone, PerNode, Standard or Premium), which are not available to all customers."
            }
        },
        "dataRetention": {
            "type": "int",
            "defaultValue": 30,
            "minValue": 7,
            "maxValue": 730,
            "metadata": {
                "description": "Number of days of retention. Workspaces in the legacy Free pricing tier can have only 7 days."
            }
        },
        "immediatePurgeDataOn30Days": {
            "type": "bool",
            "defaultValue": "[bool('false')]",
            "metadata": {
                "description": "If set to true when changing retention to 30 days, older data will be immediately deleted. Use this with extreme caution. This applies only when retention is being set to 30 days."
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
                "defaultValue": " An example runbook that gets all the Resource Manager resources by using the Run As account (service principal)."
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
                "defaultValue": " An example runbook that gets all the Resource Manager resources by using the Run As account (service principal)."
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
                "defaultValue": " An example runbook that gets all the Resource Manager resources by using the Run As account (service principal)."
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

2. Editar o modelo para satisfazer os seus requisitos. Considere criar um [ficheiro de parâmetros do Gestor](../azure-resource-manager/templates/parameter-files.md) de Recursos em vez de passar parâmetros como valores inline.

3. Guarde este ficheiro como deployAzAutomationAccttemplate.json para uma pasta local.

4. Está pronto para implementar este modelo. Pode utilizar o PowerShell ou o Azure CLI. Quando for solicitado um espaço de trabalho e nome de conta Automation, forneça um nome globalmente único em todas as suas subscrições do Azure.

    **PowerShell**

    ```powershell
    New-AzResourceGroupDeployment -Name <deployment-name> -ResourceGroupName <resource-group-name> -TemplateFile deployAzAutomationAccttemplate.json
    ```

    **CLI do Azure**

    ```cli
    az group deployment create --resource-group <my-resource-group> --name <my-deployment-name> --template-file deployAzAutomationAccttemplate.json
    ```

    O destacamento pode levar alguns minutos para terminar. Quando isso acontecer, verá uma mensagem como a seguinte que inclui o resultado.

    ![Resultado do exemplo quando a implementação está completa](media/automation-create-account-template/template-output.png)

## <a name="next-steps"></a>Passos seguintes

Agora que tem uma conta de Automação, pode criar livros de execução e automatizar processos manuais.

* Para começar com os livros de execução da PowerShell, consulte [Create a PowerShell runbook](automation-first-runbook-textual-powershell.md).
* Para começar com os livros de fluxo de trabalho PowerShell, consulte [Create a PowerShell Workflow runbook](automation-first-runbook-textual.md).
* Para começar com os livros python 2, consulte [Create a Python runbook](automation-first-runbook-textual-python2.md).