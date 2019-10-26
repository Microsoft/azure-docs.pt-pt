---
title: Criar um espaço de trabalho Log Analytics usando Azure PowerShell | Microsoft Docs
description: Saiba como criar um espaço de trabalho Log Analytics para habilitar soluções de gerenciamento e coleta de dados de seus ambientes de nuvem e locais com o Azure PowerShell.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 03/12/2019
ms.openlocfilehash: d9ac472c320767919301f5de634fd5158e824726
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2019
ms.locfileid: "72900516"
---
# <a name="create-a-log-analytics-workspace-with-azure-powershell"></a>Criar um espaço de trabalho Log Analytics com Azure PowerShell

O módulo do Azure PowerShell é utilizado para criar e gerir recursos do Azure a partir da linha de comandos do PowerShell ou em scripts. Este guia de início rápido mostra como usar o módulo Azure PowerShell para implantar um espaço de trabalho Log Analytics no Azure Monitor. Um espaço de trabalho Log Analytics é um ambiente exclusivo para dados de Azure Monitor log. Cada espaço de trabalho tem seu próprio repositório de dados e configuração, e as fontes de dados e soluções são configuradas para armazenar seus dados em um espaço de trabalho específico. Você precisará de um espaço de trabalho Log Analytics se pretender coletar dados das seguintes fontes:

* Recursos do Azure em sua assinatura  
* Computadores locais monitorados pelo System Center Operations Manager  
* Coleções de dispositivos de System Center Configuration Manager  
* Dados de diagnóstico ou de registo do armazenamento do Azure  
 
Para outras fontes, como VMs do Azure e VMs do Windows ou Linux em seu ambiente, consulte os seguintes tópicos:

* [Coletar dados de máquinas virtuais do Azure](../learn/quick-collect-azurevm.md)
* [Coletar dados de um computador Linux híbrido](../learn/quick-collect-linux-computer.md)
* [Coletar dados de um computador Windows híbrido](quick-collect-windows-computer.md)

Se você não tiver uma assinatura do Azure, crie [uma conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se você optar por instalar e usar o PowerShell localmente, este tutorial exigirá o módulo Azure PowerShell AZ. Executar `Get-Module -ListAvailable Az` para localizar a versão. Se precisar de atualizar, veja [Instalar o módulo do Azure PowerShell](/powershell/azure/install-az-ps). Se estiver a executar localmente o PowerShell, também terá de executar o `Connect-AzAccount` para criar uma ligação com o Azure.

## <a name="create-a-workspace"></a>Criar áreas de trabalho
Crie um espaço de trabalho com [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment). O exemplo a seguir cria um espaço de trabalho no local *eastus* usando um modelo do Resource Manager do computador local. O modelo JSON é configurado para solicitar apenas o nome do espaço de trabalho e especifica um valor padrão para os outros parâmetros que provavelmente seriam usados como uma configuração padrão em seu ambiente. 

Para obter informações sobre regiões com suporte, consulte [regiões log Analytics está disponível no](https://azure.microsoft.com/regions/services/) e procure por Azure monitor no campo **Pesquisar um produto** . 

Os parâmetros a seguir definem um valor padrão:

* local-o padrão é leste dos EUA
* SKU-usa como padrão o novo tipo de preço por GB lançado no modelo de preços de abril de 2018

>[!WARNING]
>Se você estiver criando ou configurando um Log Analytics espaço de trabalho em uma assinatura que tenha optado pelo novo modelo de preços de abril de 2018, o único tipo de preço válido de Log Analytics será **PerGB2018**. 
>

### <a name="create-and-deploy-template"></a>Criar e implantar modelo

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

2. Edite o modelo para atender às suas necessidades. Examine a referência de [modelo Microsoft. OperationalInsights/Workspaces](https://docs.microsoft.com/azure/templates/microsoft.operationalinsights/workspaces) para saber quais são as propriedades e os valores com suporte. 
3. Salve esse arquivo como **deploylaworkspacetemplate. JSON** em uma pasta local.   
4. Está pronto para implementar este modelo. Use os comandos a seguir da pasta que contém o modelo. Quando for solicitado um nome de espaço de trabalho, forneça um nome que seja globalmente exclusivo em todas as assinaturas do Azure.

    ```powershell
        New-AzResourceGroupDeployment -Name <deployment-name> -ResourceGroupName <resource-group-name> -TemplateFile deploylaworkspacetemplate.json
    ```

A implementação pode demorar alguns minutos a concluir. Quando ele for concluído, você verá uma mensagem semelhante à seguinte que inclui o resultado:

![Resultado de exemplo quando a implantação é concluída](media/quick-create-workspace-posh/template-output-01.png)

## <a name="next-steps"></a>Passos seguintes
Agora que você tem um espaço de trabalho disponível, é possível configurar a coleta de telemetria de monitoramento, executar pesquisas de log para analisar esses dados e adicionar uma solução de gerenciamento para fornecer dados adicionais e informações analíticas.  

* Para habilitar a coleta de dados de recursos do Azure com o Diagnóstico do Azure ou o armazenamento do Azure, consulte [coletar logs e métricas de serviço do Azure para uso no Azure monitor](../platform/collect-azure-metrics-logs.md).  
* Adicione [System Center Operations Manager como uma fonte de dados](../platform/om-agents.md) para coletar dados de agentes que relatam seu grupo de gerenciamento de Operations Manager e armazená-los em seu espaço de trabalho do log Analytics.  
* Conecte [Configuration Manager](../platform/collect-sccm.md) para importar computadores que são membros de coleções na hierarquia.  
* Examine as [soluções de monitoramento](../insights/solutions.md) disponíveis e como adicionar ou remover uma solução do seu espaço de trabalho.
