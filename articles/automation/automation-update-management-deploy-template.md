---
title: Ativar a gestão de atualização usando o modelo do Gestor de Recursos Azure ; Microsoft Docs
description: Este artigo diz como usar um modelo de Gestor de Recursos Azure para ativar a Gestão de Atualização.
ms.service: automation
ms.subservice: update-management
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 06/10/2020
ms.openlocfilehash: feb1cc132bf5463550a2e7921f347c8f2f48260e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84668003"
---
# <a name="enable-update-management-using-azure-resource-manager-template"></a>Ativar a Gestão de Atualizações com o modelo do Azure Resource Manager

Pode utilizar um [modelo de Gestor de Recursos Azure](../azure-resource-manager/templates/template-syntax.md) para ativar a funcionalidade de Gestão de Atualização da Automação Azure no seu grupo de recursos. Este artigo fornece um modelo de amostra que automatiza o seguinte:

* Criação de um espaço de trabalho Azure Monitor Log Analytics.
* Criação de uma conta Azure Automation.
* Ligando a conta Automation ao espaço de trabalho Log Analytics, se não já estiver ligado.
* Ativar a Gestão de Atualização.

O modelo não automatiza a gestão de atualização em um ou mais VMs Azure ou não-Azure.

Se já tem um espaço de trabalho do Log Analytics e uma conta de Automação implantada numa região suportada na sua subscrição, não estão ligados. A utilização deste modelo cria com sucesso a ligação e implementa a Gestão de Atualização.

## <a name="api-versions"></a>Versões da API

A tabela que se segue lista as versões API para os recursos utilizados neste modelo.

| Recurso | Tipo de recurso | Versão API |
|:---|:---|:---|
| Área de trabalho | áreas de trabalho | Antevisão 2020-03-01 |
| Conta de automatização | automation | 2018-06-30 | 
| Solução | soluções | Antevisão 2015-11-01 |

## <a name="before-using-the-template"></a>Antes de usar o modelo

Se optar por instalar e utilizar o PowerShell localmente, este artigo requer o módulo Azure PowerShell Az. Executar `Get-Module -ListAvailable Az` para localizar a versão. Se precisar de atualizar, veja [Instalar o módulo do Azure PowerShell](/powershell/azure/install-az-ps). Se estiver a executar o PowerShell localmente, também precisa de executar [o Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.7.0) para criar uma ligação com o Azure. Com a Azure PowerShell, a implementação utiliza [o New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment).

Se optar por instalar e utilizar o CLI localmente, este artigo requer que esteja a executar a versão 2.1.0 ou mais tarde do Azure CLI. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). Com o Azure CLI, esta implementação utiliza [a criação de implementação de grupo az](https://docs.microsoft.com/cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-create). 

O modelo JSON está configurado para o solicitar para:

* O nome do espaço de trabalho.
* A região onde se cria o espaço de trabalho.
* Para ativar permissões de recursos ou espaço de trabalho.
* O nome da conta Automation.
* A região onde criar a conta.

O modelo JSON especifica um valor padrão para os outros parâmetros que são suscetíveis de ser usados para uma configuração padrão no seu ambiente. Pode armazenar o modelo numa conta de armazenamento Azure para acesso partilhado na sua organização. Para obter mais informações sobre o trabalho com os modelos, consulte [implementar recursos com modelos de Gestor de Recursos e Azure CLI](../azure-resource-manager/templates/deploy-cli.md).

Os seguintes parâmetros no modelo são definidos com um valor padrão para o espaço de trabalho Log Analytics:

* sku - incumprimentos ao novo nível de preços Per-GB lançado no modelo de preços de abril de 2018
* retenção de dados - incumprimentos de trinta dias

>[!WARNING]
>Se criar ou configurar um espaço de trabalho Log Analytics numa subscrição que optou pelo novo modelo de preços de abril de 2018, o único nível de preços válido do Log Analytics é **o PerGB2018**.
>

O modelo JSON especifica um valor padrão para os outros parâmetros que provavelmente seriam usados como uma configuração padrão no seu ambiente. Pode armazenar o modelo numa conta de armazenamento Azure para acesso partilhado na sua organização. Para obter mais informações sobre o trabalho com os modelos, consulte [implementar recursos com modelos de Gestor de Recursos e Azure CLI](../azure-resource-manager/templates/deploy-cli.md).

É importante compreender os seguintes detalhes de configuração se é novo no Azure Automation e no Azure Monitor, de forma a evitar erros ao tentar criar, configurar e utilizar um espaço de trabalho Log Analytics ligado à sua nova conta Automation.

* Reveja [detalhes adicionais](../azure-monitor/platform/template-workspace-configuration.md#create-a-log-analytics-workspace) para entender completamente as opções de configuração do espaço de trabalho, tais como o modo de controlo de acesso, o nível de preços, a retenção e o nível de reserva de capacidade.

* Como apenas certas regiões são suportadas para ligar um espaço de trabalho Log Analytics e uma conta Automation na sua subscrição, [reveja os mapeamentos do Workspace](how-to/region-mappings.md) para especificar as regiões suportadas em linha ou num ficheiro de parâmetros.

* Se você é novo em registos do Azure Monitor e ainda não implementou um espaço de trabalho, você deve rever a orientação de design do [espaço de trabalho](../azure-monitor/platform/design-logs-deployment.md) para aprender sobre o controle de acesso, e entender as estratégias de implementação do design que recomendamos para a sua organização.

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
                    "apiVersion": "2015-11-01-preview",
                    "location": "[parameters('location')]",
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
            "apiVersion": "2018-06-30",
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
    ```

2. Edite o modelo para satisfazer os seus requisitos. Considere criar um [ficheiro de parâmetros do Gestor](../azure-resource-manager/templates/parameter-files.md) de Recursos em vez de passar parâmetros como valores inline.

3. Guarde este ficheiro numa pasta local à medida **quedeployUMSolutiontemplate.jsligado**.

4. Está pronto para implementar este modelo. Pode utilizar o PowerShell ou o Azure CLI. Quando você é solicitado para um espaço de trabalho e nome de conta Automation, forneça um nome que é globalmente único em todas as subscrições do Azure.

    **PowerShell**

    ```powershell
    New-AzResourceGroupDeployment -Name <deployment-name> -ResourceGroupName <resource-group-name> -TemplateFile deployUMSolutiontemplate.json
    ```

    **CLI do Azure**

    ```cli
    az group deployment create --resource-group <my-resource-group> --name <my-deployment-name> --template-file deployUMSolutiontemplate.json
    ```

    A implementação pode demorar alguns minutos a concluir. Quando termina, vê uma mensagem semelhante à seguinte que inclui o resultado:

    ![Exemplo resultado quando a implementação está completa](media/automation-update-management-deploy-template/template-output.png)

## <a name="next-steps"></a>Próximos passos

* Para utilizar a Gestão de Atualização para VMs, consulte [Gerir atualizações e patches para os seus VMs Azure](automation-tutorial-update-management.md).

* Se já não necessitar do espaço de trabalho Do Log Analytics, consulte instruções no [espaço de trabalho Unlink da Conta de Automação para Gestão de Atualização.](automation-unlink-workspace-update-management.md)

* Para eliminar VMs da Gestão de Atualização, consulte [remover VMs da Gestão de Atualização](automation-remove-vms-from-update-management.md).
