---
title: Criar uma área de trabalho do Log Analytics com o Azure PowerShell | Documentos da Microsoft
description: Saiba como criar uma área de trabalho do Log Analytics para ativar a recolha de dados e soluções de gerenciamento de seus ambientes na cloud e no local com o Azure PowerShell.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/12/2019
ms.openlocfilehash: 303f255057b414bc06cd7ae803fe368c2acc1a1a
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75399454"
---
# <a name="create-a-log-analytics-workspace-with-azure-powershell"></a>Criar uma área de trabalho do Log Analytics com o Azure PowerShell

O módulo do Azure PowerShell é utilizado para criar e gerir recursos do Azure a partir da linha de comandos do PowerShell ou em scripts. Este guia de início rápido mostra como usar o módulo Azure PowerShell para implantar um espaço de trabalho Log Analytics no Azure Monitor. Um espaço de trabalho Log Analytics é um ambiente exclusivo para dados de Azure Monitor log. Cada espaço de trabalho tem seu próprio repositório de dados e configuração, e as fontes de dados e soluções são configuradas para armazenar seus dados em um espaço de trabalho específico. Você precisará de um espaço de trabalho Log Analytics se pretender coletar dados das seguintes fontes:

* Recursos do Azure na sua subscrição  
* Computadores monitorizados pelo System Center Operations Manager no local  
* Coleções de dispositivos do System Center Configuration Manager  
* Dados de diagnóstico ou de registo do armazenamento do Azure  
 
Para outras origens, como as VMs do Azure e o Windows ou VMs do Linux no seu ambiente, consulte os seguintes tópicos:

* [Recolher dados de máquinas virtuais do Azure](../learn/quick-collect-azurevm.md)
* [Recolher dados de computador com Linux híbrida](../learn/quick-collect-linux-computer.md)
* [Recolher dados do computador de Windows híbrida](quick-collect-windows-computer.md)

Se não tiver uma subscrição do Azure, crie [uma conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se você optar por instalar e usar o PowerShell localmente, este tutorial exigirá o módulo Azure PowerShell AZ. Executar `Get-Module -ListAvailable Az` para localizar a versão. Se precisar de atualizar, veja [Instalar o módulo do Azure PowerShell](/powershell/azure/install-az-ps). Se estiver a executar localmente o PowerShell, também terá de executar o `Connect-AzAccount` para criar uma ligação com o Azure.

## <a name="create-a-workspace"></a>Criar áreas de trabalho
Crie um espaço de trabalho com [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment). O exemplo a seguir cria um espaço de trabalho no local *eastus* usando um modelo do Resource Manager do computador local. O modelo JSON está configurado para apenas solicitar-lhe o nome da área de trabalho e especifica um valor predefinido para os outros parâmetros que provavelmente seria usado como uma configuração padrão no seu ambiente. 

Para obter informações sobre regiões com suporte, consulte [regiões log Analytics está disponível no](https://azure.microsoft.com/regions/services/) e procure por Azure monitor no campo **Pesquisar um produto** . 

Os seguintes parâmetros de definir um valor predefinido:

* localização – a predefinição é E.U.A. leste
* SKU - é predefinido para o escalão de preço por GB novo lançado no modelo de preços de Abril de 2018

>[!WARNING]
>Se criar ou configurar uma área de trabalho do Log Analytics numa subscrição que tenha optado pelo modelo de preços de Abril de 2018 novo, o escalão de preço de Log Analytics só é válida é **PerGB2018**. 
>

### <a name="create-and-deploy-template"></a>Criar e implementar modelo

1. Copie e cole a seguinte sintaxe JSON no seu ficheiro:

    ```json
    {
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceName": {
            "type": "String",
            "metadata": {
              "description": "Specifies the name of the workspace."
            }
        },
        "location": {
            "type": "String",
            "allowedValues": [
              "eastus",
              "westus"
            ],
            "defaultValue": "eastus",
            "metadata": {
              "description": "Specifies the location in which to create the workspace."
            }
        },
        "sku": {
            "type": "String",
            "allowedValues": [
              "Standalone",
              "PerNode",
              "PerGB2018"
            ],
            "defaultValue": "PerGB2018",
            "metadata": {
            "description": "Specifies the service tier of the workspace: Standalone, PerNode, Per-GB"
        }
          }
    },
    "resources": [
        {
            "type": "Microsoft.OperationalInsights/workspaces",
            "name": "[parameters('workspaceName')]",
            "apiVersion": "2015-11-01-preview",
            "location": "[parameters('location')]",
            "properties": {
                "sku": {
                    "Name": "[parameters('sku')]"
                },
                "features": {
                    "searchVersion": 1
                }
            }
          }
       ]
    }
    ```

2. Edite o modelo para satisfazer os seus requisitos. Revisão [Microsoft.OperationalInsights/workspaces modelo](https://docs.microsoft.com/azure/templates/microsoft.operationalinsights/workspaces) referência para saber quais propriedades e valores são suportados. 
3. Guarde este ficheiro como **deploylaworkspacetemplate.json** para uma pasta local.   
4. Está pronto para implementar este modelo. Use os comandos a seguir da pasta que contém o modelo. Quando for solicitado um nome de espaço de trabalho, forneça um nome que seja globalmente exclusivo em todas as assinaturas do Azure.

    ```powershell
        New-AzResourceGroupDeployment -Name <deployment-name> -ResourceGroupName <resource-group-name> -TemplateFile deploylaworkspacetemplate.json
    ```

A implementação pode demorar alguns minutos a concluir. Quando terminar, verá uma mensagem semelhante ao seguinte, que inclui o resultado:

![Exemplo de resultado quando a implementação estiver concluída](media/quick-create-workspace-posh/template-output-01.png)

## <a name="next-steps"></a>Passos seguintes
Agora que tem uma área de trabalho disponível, pode configurar a recolha de monitorização de telemetria, executar pesquisas de registos para analisar esses dados e adicionar uma solução de gestão para fornecer dados adicionais e informações de análise.  

* Para habilitar a coleta de dados de recursos do Azure com o Diagnóstico do Azure ou o armazenamento do Azure, consulte [coletar logs e métricas de serviço do Azure para uso no Azure monitor](../platform/collect-azure-metrics-logs.md).  
* Adicione [System Center Operations Manager como origem de dados](../platform/om-agents.md) para recolher dados de agentes que reportam o grupo de gestão do Operations Manager e o armazenamos em sua área de trabalho do Log Analytics.  
* Ligue-se [Configuration Manager](../platform/collect-sccm.md) para importar computadores que são membros de coleções na hierarquia.  
* Examine as [soluções de monitoramento](../insights/solutions.md) disponíveis e como adicionar ou remover uma solução do seu espaço de trabalho.
