---
title: Ativar a gestão de atualização usando o modelo do Gestor de Recursos Azure ; Microsoft Docs
description: Este artigo diz como usar um modelo de Gestor de Recursos Azure para ativar a Gestão de Atualização.
ms.service: automation
ms.subservice: update-management
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 09/18/2020
ms.openlocfilehash: 148ed0eab3e74d96ea6dbf1f507cd0ea53ad2fdc
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/15/2020
ms.locfileid: "92073789"
---
# <a name="enable-update-management-using-azure-resource-manager-template"></a>Ativar a Gestão de Atualizações com o modelo do Azure Resource Manager

Pode utilizar um [modelo de Gestor de Recursos Azure](../../azure-resource-manager/templates/template-syntax.md) para ativar a funcionalidade de Gestão de Atualização da Automação Azure no seu grupo de recursos. Este artigo fornece um modelo de amostra que automatiza o seguinte:

* Automatiza a criação de um espaço de trabalho Azure Monitor Log Analytics.
* Automatiza a criação de uma conta Azure Automation.
* Liga a conta Automation à conta Despassição ao espaço de trabalho Do Log Analytics.
* Adiciona livros de amostra de automatização à conta.
* Ativa a função de Gestão de Atualização.

O modelo não automatiza a gestão de atualização em um ou mais VMs Azure ou não-Azure.

Se já tem um espaço de trabalho do Log Analytics e uma conta de Automação implantada numa região suportada na sua subscrição, não estão ligados. A utilização deste modelo cria com sucesso a ligação e implementa a Gestão de Atualização.

>[!NOTE]
>Criação da automatização Executada Como a conta não é suportada quando se está a utilizar um modelo ARM. Para criar uma conta Run As manualmente a partir do portal ou com o PowerShell, consulte [Gerir como contas](../manage-runas-account.md).

Depois de completar estes passos, precisa de [configurar as definições](../automation-manage-send-joblogs-log-analytics.md) de diagnóstico para a sua conta Demôm automação para enviar o estado de trabalho do runbook e os fluxos de trabalho para o espaço de trabalho do Log Analytics ligado.

## <a name="api-versions"></a>Versões da API

A tabela que se segue lista a versão API para os recursos utilizados neste exemplo.

| Recurso | Tipo de recurso | Versão API |
|:---|:---|:---|
| [Área de trabalho](/azure/templates/microsoft.operationalinsights/workspaces) | áreas de trabalho | Antevisão 2020-03-01 |
| [Conta de automação](/azure/templates/microsoft.automation/automationaccounts) | automatização | Antevisão 2020-01-13 |
| [Serviços ligados ao espaço de trabalho](/azure/templates/microsoft.operationalinsights/workspaces/linkedservices) | áreas de trabalho | Antevisão 2020-03-01 |
| [Soluções](/azure/templates/microsoft.operationsmanagement/solutions) | soluções | Antevisão 2015-11-01 |

## <a name="before-using-the-template"></a>Antes de usar o modelo

O modelo JSON está configurado para o solicitar para:

* O nome do espaço de trabalho.
* A região para criar o espaço de trabalho em.
* O nome da conta Automation.
* A região para criar a conta Automation in.

Os seguintes parâmetros no modelo são definidos com um valor padrão para o espaço de trabalho Log Analytics:

* *sku* incumprimentos ao nível de preços por GB lançado no modelo de preços de abril de 2018.
* *dataRetention* predefinição para 30 dias.

>[!WARNING]
>Se pretender criar ou configurar um espaço de trabalho Log Analytics numa subscrição que tenha optado pelo modelo de preços de abril de 2018, o único nível de preços válido do Log Analytics é *o PerGB2018.*
>

O modelo JSON especifica um valor padrão para os outros parâmetros que provavelmente seriam usados como uma configuração padrão no seu ambiente. Pode armazenar o modelo numa conta de armazenamento Azure para acesso partilhado na sua organização. Para obter mais informações sobre o trabalho com os modelos, consulte [recursos de implantação com modelos ARM e o Azure CLI](../../azure-resource-manager/templates/deploy-cli.md).

Se é novo na Azure Automation e Azure Monitor, é importante que compreenda os seguintes detalhes de configuração. Podem ajudá-lo a evitar erros quando tenta criar, configurar e utilizar um espaço de trabalho Log Analytics ligado à sua nova conta Automation.

* Reveja [detalhes adicionais](../../azure-monitor/samples/resource-manager-workspace.md#create-a-log-analytics-workspace) para entender completamente as opções de configuração do espaço de trabalho, tais como o modo de controlo de acesso, o nível de preços, a retenção e o nível de reserva de capacidade.

* Reveja [os mapeamentos do espaço de trabalho](../how-to/region-mappings.md) para especificar as regiões suportadas em linha ou num ficheiro de parâmetros. Apenas algumas regiões são suportadas para ligar um espaço de trabalho Log Analytics e uma conta Automation na sua subscrição.

* Se é novo nos registos do Azure Monitor e ainda não implementou um espaço de trabalho, deverá rever a [orientação](../../azure-monitor/platform/design-logs-deployment.md)do design do espaço de trabalho . Irá ajudá-lo a aprender sobre o controlo de acessos e a compreender as estratégias de implementação do design que recomendamos para a sua organização.

## <a name="deploy-template"></a>Implementar o modelo

1. Copie e cole a seguinte sintaxe JSON no seu ficheiro:

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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
                "defaultValue": "[resourceGroup().location]",
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
                    "description": "Specifies the location in which to create the Automation account."
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
            "samplePowerShellRunbookName": {
                "type": "String",
                "defaultValue": "AzureAutomationTutorialScript"
            },
            "samplePowerShellRunbookDescription": {
                "type": "String",
                "defaultValue": " An example runbook that gets all the Resource Manager resources by using the Run As account (service principal)."
            },
            "samplePython2RunbookName": {
                "type": "String",
                "defaultValue": "AzureAutomationTutorialPython2"
            },
            "samplePython2RunbookDescription": {
                "type": "String",
                "defaultValue": " An example runbook that gets all the Resource Manager resources by using the Run As account (service principal)."
            },
            "_artifactsLocation": {
                "type": "string",
                "defaultValue": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-automation/",
                "metadata": {
                    "description": "URI to artifacts location"
                }
            },
            "_artifactsLocationSasToken": {
                "type": "securestring",
                "defaultValue": "",
                "metadata": {
                    "description": "The sasToken required to access _artifactsLocation.  When the template is deployed using the accompanying scripts, a sasToken will be automatically generated"
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
                "apiVersion": "2020-03-01-preview",
                "name": "[parameters('workspaceName')]",
                "location": "[parameters('location')]",
                "properties": {
                    "sku": {
                        "name": "[parameters('sku')]"
                    },
                    "retentionInDays": "[parameters('dataRetention')]",
                    "features": {
                        "searchVersion": 1,
                        "legacy": 0
                    }
                }
            },
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
            },
            {
                "type": "Microsoft.Automation/automationAccounts",
                "apiVersion": "2020-01-13-preview",
                "name": "[parameters('automationAccountName')]",
                "location": "[parameters('automationAccountLocation')]",
                "dependsOn": [
                    "[parameters('workspaceName')]"
                ],
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
                            "[parameters('automationAccountName')]"
                        ],
                        "properties": {
                            "runbookType": "GraphPowerShell",
                            "logProgress": "false",
                            "logVerbose": "false",
                            "description": "[parameters('sampleGraphicalRunbookDescription')]",
                            "publishContentLink": {
                                "uri": "[uri(parameters('_artifactsLocation'), concat('scripts/AzureAutomationTutorial.graphrunbook', parameters('_artifactsLocationSasToken')))]",
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
                            "[parameters('automationAccountName')]"
                        ],
                        "properties": {
                            "runbookType": "PowerShell",
                            "logProgress": "false",
                            "logVerbose": "false",
                            "description": "[parameters('samplePowerShellRunbookDescription')]",
                            "publishContentLink": {
                                "uri": "[uri(parameters('_artifactsLocation'), concat('scripts/AzureAutomationTutorial.ps1', parameters('_artifactsLocationSasToken')))]",
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
                            "[parameters('automationAccountName')]"
                        ],
                        "properties": {
                            "runbookType": "Python2",
                            "logProgress": "false",
                            "logVerbose": "false",
                            "description": "[parameters('samplePython2RunbookDescription')]",
                            "publishContentLink": {
                                "uri": "[uri(parameters('_artifactsLocation'), concat('scripts/AzureAutomationTutorialPython2.py', parameters('_artifactsLocationSasToken')))]",
                                "version": "1.0.0.0"
                            }
                        }
                    }
                ]
            },
            {
                "type": "Microsoft.OperationalInsights/workspaces/linkedServices",
                "apiVersion": "2020-03-01-preview",
                "name": "[concat(parameters('workspaceName'), '/' , 'Automation')]",
                "location": "[parameters('location')]",
                "dependsOn": [
                    "[parameters('workspaceName')]",
                    "[parameters('automationAccountName')]"
                ],
                "properties": {
                    "resourceId": "[resourceId('Microsoft.Automation/automationAccounts', parameters('automationAccountName'))]"
                }
            }
        ]
    }
    ```

2. Edite o modelo para satisfazer os seus requisitos. Considere criar um [ficheiro de parâmetros do Gestor](../../azure-resource-manager/templates/parameter-files.md) de Recursos em vez de passar parâmetros como valores inline.

3. Guarde este ficheiro numa pasta local à medida ** quedeployUMSolutiontemplate.jsligado**.

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

    ![Exemplo resultado quando a implementação está completa](media/update-mgmt-enable-template/template-output.png)

## <a name="review-deployed-resources"></a>Revisão dos recursos implantados

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

2. No portal Azure, abra a conta Automation que criou.

3. A partir do painel esquerdo, selecione **Runbooks**. Na página **Runbooks,** listadas estão três runbooks tutoriais criados com a conta Automation.

    ![Livros de execução tutoriais criados com conta Automation](../media/quickstart-create-automation-account-template/automation-sample-runbooks.png)

4. A partir do painel de esquerda, selecione **Linked workspace**. Na página do **espaço de trabalho Linked,** mostra o espaço de trabalho Log Analytics especificado anteriormente ligado à sua conta Automation.

    ![Conta de automação ligada ao espaço de trabalho Log Analytics](../media/quickstart-create-automation-account-template/automation-account-linked-workspace.png)

5. A partir do painel de esquerda, selecione **A gestão de Atualização**. Na página de gestão de **Atualização,** mostra a página de avaliação sem qualquer informação como resultado de apenas ser ativada, e as máquinas não estão configuradas para gestão.

    ![Vista de avaliação de recursos de gestão de atualização](./media/update-mgmt-enable-template/update-management-assessment-view.png)

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não precisar, elimine a solução De Atualizações no espaço de trabalho Do Log Analytics, **desvincula** a conta Demômpacial do espaço de trabalho e, em seguida, apaga a conta de Automação e espaço de trabalho.

## <a name="next-steps"></a>Passos seguintes

* Para utilizar a Gestão de Atualização para VMs, consulte [Gerir atualizações e patches para os seus VMs](update-mgmt-manage-updates-for-vm.md).

* Se já não pretender utilizar a Gestão de Atualização e pretender removê-la, consulte instruções na [função 'Remover a actualização'](update-mgmt-remove-feature.md)

* Para eliminar VMs da Gestão de Atualização, consulte [remover VMs da Gestão de Atualização](update-mgmt-remove-vms.md).