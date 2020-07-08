---
title: Criar uma conta de Automação utilizando um modelo de Gestor de Recursos Azure ! Microsoft Docs
description: Este artigo diz como usar um modelo de Gestor de Recursos Azure para criar uma conta Azure Automation.
ms.service: automation
ms.subservice: update-management
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 06/09/2020
ms.openlocfilehash: 6b26db522db246add48941da9af4784ed2942a0a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84661026"
---
# <a name="create-an-automation-account-using-an-azure-resource-manager-template"></a>Criar uma conta de Automação utilizando um modelo de Gestor de Recursos Azure

Pode utilizar [os modelos do Gestor de Recursos Azure](../azure-resource-manager/templates/template-syntax.md) para criar uma conta Azure Automation no seu grupo de recursos. Este artigo fornece um modelo de amostra que:

* Automatiza a criação de um espaço de trabalho Azure Monitor Log Analytics.
* Automatiza a criação de uma conta Azure Automation.
* Liga a conta Automation à conta Despassição ao espaço de trabalho Do Log Analytics.

O modelo não automatiza a ativação de máquinas virtuais Azure ou não-Azure. 

>[!NOTE]
>Criação da Gestão de Automação Como conta não é suportada quando está a utilizar um modelo de Gestor de Recursos Azure. Para criar uma conta Run As manualmente a partir do portal ou com o PowerShell, consulte [Gerir como contas](manage-runas-account.md).

Depois de completar estes passos, precisa de [configurar as definições](automation-manage-send-joblogs-log-analytics.md) de diagnóstico para a sua conta Demôm automação para enviar o estado de trabalho do runbook e os fluxos de trabalho para o espaço de trabalho do Log Analytics ligado. 

## <a name="api-versions"></a>Versões da API

A tabela que se segue lista a versão API para os recursos utilizados neste exemplo.

| Recurso | Tipo de recurso | Versão API |
|:---|:---|:---|
| Área de trabalho | áreas de trabalho | Antevisão 2020-03-01 |
| Conta de automatização | automation | 2018-06-30 | 

## <a name="before-you-use-the-template"></a>Antes de usar o modelo

Se optar por instalar e utilizar o PowerShell localmente, este artigo requer o módulo Azure PowerShell Az. Executar `Get-Module -ListAvailable Az` para localizar a versão. Se precisar de atualizar, veja [Instalar o módulo do Azure PowerShell](/powershell/azure/install-az-ps). Se estiver a executar o PowerShell localmente, também precisa de correr `Connect-AzAccount` para criar uma ligação com o Azure. Com o PowerShell, a implementação utiliza [o New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment).

Se optar por instalar e utilizar o Azure CLI localmente, este artigo requer que esteja a executar a versão 2.1.0 ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). Com o Azure CLI, esta implementação utiliza [a criação de grupo az](https://docs.microsoft.com/cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-create). 

O modelo JSON está configurado para o solicitar para:

* O nome do espaço de trabalho.
* A região para criar o espaço de trabalho em.
* Para ativar permissões de recursos ou espaço de trabalho.
* O nome da conta Automation.
* A região para criar a conta Automation in.

Os seguintes parâmetros no modelo são definidos com um valor padrão para o espaço de trabalho Log Analytics:

* *sku* incumprimentos ao nível de preços por GB lançado no modelo de preços de abril de 2018.
* *dataRetention* predefinição para 30 dias.

>[!WARNING]
>Se pretender criar ou configurar um espaço de trabalho Log Analytics numa subscrição que tenha optado pelo modelo de preços de abril de 2018, o único nível de preços válido do Log Analytics é *o PerGB2018.*
>

O modelo JSON especifica um valor padrão para os outros parâmetros que provavelmente seriam usados como uma configuração padrão no seu ambiente. Pode armazenar o modelo numa conta de armazenamento Azure para acesso partilhado na sua organização. Para obter mais informações sobre o trabalho com os modelos, consulte [implementar recursos com modelos de Gestor de Recursos e o CLI Azure](../azure-resource-manager/templates/deploy-cli.md).

Se é novo na Azure Automation e Azure Monitor, é importante que compreenda os seguintes detalhes de configuração. Podem ajudá-lo a evitar erros quando tenta criar, configurar e utilizar um espaço de trabalho Log Analytics ligado à sua nova conta Automation.

* Reveja [detalhes adicionais](../azure-monitor/platform/template-workspace-configuration.md#create-a-log-analytics-workspace) para entender completamente as opções de configuração do espaço de trabalho, tais como o modo de controlo de acesso, o nível de preços, a retenção e o nível de reserva de capacidade.

* Reveja [os mapeamentos do espaço de trabalho](how-to/region-mappings.md) para especificar as regiões suportadas em linha ou num ficheiro de parâmetros. Apenas algumas regiões são suportadas para ligar um espaço de trabalho Log Analytics e uma conta Automation na sua subscrição.

* Se é novo nos registos do Azure Monitor e ainda não implementou um espaço de trabalho, deverá rever a [orientação](../azure-monitor/platform/design-logs-deployment.md)do design do espaço de trabalho . Irá ajudá-lo a aprender sobre o controlo de acessos e a compreender as estratégias de implementação do design que recomendamos para a sua organização.

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
                "description": "Number of days to retain data."
            }
        },
        "location": {
            "type": "string",
            "metadata": {
                "description": "Specifies the location in which to create the workspace."
            }
        },
        "resourcePermissions": {
              "type": "bool",
              "metadata": {
                "description": "true to use resource or workspace permissions. false to require workspace permissions."
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
            "apiVersion": "2020-03-01-preview",
            "location": "[parameters('location')]",
            "properties": {
                "sku": {
                    "name": "[parameters('sku')]",
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
            "apiVersion": "2018-06-30",
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
                        "apiVersion": "2018-06-30",
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
                        "apiVersion": "2018-06-30",
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
                        "apiVersion": "2018-06-30",
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
            "apiVersion": "2020-03-01-preview",
            "type": "Microsoft.OperationalInsights/workspaces/linkedServices",
            "name": "[concat(parameters('workspaceName'), '/' , 'Automation')]",
            "location": "[parameters('location')]",
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

2. Edite o modelo para satisfazer os seus requisitos. Considere criar um [ficheiro de parâmetro do Gestor](../azure-resource-manager/templates/parameter-files.md) de Recursos em vez de passar parâmetros como valores inline.

3. Guarde este ficheiro à medida que deployAzAutomationAccttemplate.jsnuma pasta local.

4. Está pronto para implementar este modelo. Pode utilizar o PowerShell ou o Azure CLI. Quando você é solicitado para um espaço de trabalho e nome de conta Demôm automação, forneça um nome que é globalmente único em todas as suas subscrições Azure.

    **PowerShell**

    ```powershell
    New-AzResourceGroupDeployment -Name <deployment-name> -ResourceGroupName <resource-group-name> -TemplateFile deployAzAutomationAccttemplate.json
    ```

    **CLI do Azure**

    ```cli
    az group deployment create --resource-group <my-resource-group> --name <my-deployment-name> --template-file deployAzAutomationAccttemplate.json
    ```

    A colocação pode levar alguns minutos para terminar. Quando isso acontecer, verá uma mensagem como a seguinte que inclui o resultado.

    ![Exemplo resultado quando a implementação está completa](media/automation-create-account-template/template-output.png)

## <a name="next-steps"></a>Próximos passos

Para encaminhar o estado de trabalho do runbook e os fluxos de emprego para o seu espaço de trabalho linked's Log Analytics, reveja [os dados de trabalho da Forward Azure Automation para os registos do Azure Monitor](automation-manage-send-joblogs-log-analytics.md). Isto configura as definições de diagnóstico da conta Automation utilizando comandos Azure PowerShell para completar a integração para envio de registos para o espaço de trabalho para análise. 
