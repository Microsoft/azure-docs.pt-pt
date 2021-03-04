---
title: Crie um espaço de trabalho Log Analytics utilizando o Azure CLI | Microsoft Docs
description: Aprenda a criar um espaço de trabalho Log Analytics para permitir soluções de gestão e recolha de dados a partir de ambientes de nuvem e no local com O Azure CLI.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/26/2020
ms.openlocfilehash: 8e1fc816e32e563161e1604bdcd7a7006353e4ed
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102047388"
---
# <a name="create-a-log-analytics-workspace-with-azure-cli-20"></a>Criar um espaço de trabalho Log Analytics com Azure CLI 2.0

A CLI 2.0 do Azure é utilizada para criar e gerir recursos do Azure a partir da linha de comandos ou em scripts. Este quickstart mostra-lhe como usar o Azure CLI 2.0 para implantar um espaço de trabalho Log Analytics no Azure Monitor. Um espaço de trabalho Log Analytics é um ambiente único para os dados de registo do Azure Monitor. Cada espaço de trabalho tem o seu próprio repositório de dados e configuração, e as fontes de dados e soluções são configuradas para armazenar os seus dados num determinado espaço de trabalho. Necessita de um espaço de trabalho Log Analytics se pretender recolher dados a partir das seguintes fontes:

* Recursos Azure na sua subscrição  
* Computadores no local monitorizados pelo Gestor de Operações do Centro de Sistema  
* Coleções de dispositivos do Gestor de Configuração  
* Dados de diagnóstico ou de registo do armazenamento do Azure  

Para outras fontes, tais como VMs Azure e Windows ou VMs Linux no seu ambiente, consulte os seguintes tópicos:

* [Recolher dados de máquinas virtuais Azure](../vm/quick-collect-azurevm.md)
* [Recolher dados do computador híbrido Linux](../vm/quick-collect-linux-computer.md)
* [Recolher dados do computador híbrido Windows](../vm/quick-collect-windows-computer.md)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- Este artigo requer a versão 2.0.30 ou posterior do Azure CLI. Se utilizar o Azure Cloud Shell, a versão mais recente já está instalada.

## <a name="create-a-workspace"></a>Criar uma área de trabalho
Criar um espaço de trabalho com [o grupo de implantação az criar](/cli/azure/deployment/group#az_deployment_group_create). O exemplo a seguir cria um espaço de trabalho na localização *leste* usando um modelo de Gestor de Recursos a partir da sua máquina local. O modelo JSON é configurado apenas para o solicitar o nome do espaço de trabalho, e especifica um valor padrão para os outros parâmetros que provavelmente seriam usados como uma configuração padrão no seu ambiente. Ou pode armazenar o modelo numa conta de armazenamento Azure para acesso partilhado na sua organização. Para mais informações sobre o trabalho com os modelos, consulte [recursos de implementação com modelos de Gestor de Recursos e CLI Azure](../../azure-resource-manager/templates/deploy-cli.md)

Para obter informações sobre as regiões suportadas, consulte [as regiões Log Analytics](https://azure.microsoft.com/regions/services/) e procure o Azure Monitor a partir da pesquisa de um campo de **produtos.**

Os seguintes parâmetros definem um valor predefinido:

* localização - incumprimentos para leste dos EUA
* sku - incumprimentos ao novo nível de preços Per-GB lançado no modelo de preços de abril de 2018

>[!WARNING]
>Se criar ou configurar um espaço de trabalho Log Analytics numa subscrição que optou pelo novo modelo de preços de abril de 2018, o único nível de preços válido do Log Analytics é **o PerGB2018**.
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

2. Edite o modelo para satisfazer os seus requisitos. Reveja a referência [do modelo Microsoft.OperationalInsights/workspaces](/azure/templates/microsoft.operationalinsights/2015-11-01-preview/workspaces) para saber quais as propriedades e valores suportados.
3. Guarde este ficheiro à medida **quedeploylaworkspacetemplate.jsnuma** pasta local.   
4. Está pronto para implementar este modelo. Utilize os seguintes comandos da pasta que contém o gabarito. Quando você é solicitado para um nome de espaço de trabalho, forneça um nome que é globalmente único em todas as subscrições do Azure.

    ```azurecli
    az deployment group create --resource-group <my-resource-group> --name <my-deployment-name> --template-file deploylaworkspacetemplate.json
    ```

A implementação pode demorar alguns minutos a concluir. Quando termina, vê uma mensagem semelhante à seguinte que inclui o resultado:

![Exemplo resultado quando a implementação está completa](media/quick-create-workspace-cli/template-output-01.png)

## <a name="troubleshooting"></a>Resolução de problemas
Quando cria um espaço de trabalho que foi eliminado nos últimos 14 dias e em [estado de eliminação suave,](../logs/delete-workspace.md#soft-delete-behavior)a operação pode ter resultados diferentes dependendo da configuração do seu espaço de trabalho:
1. Se fornecer o mesmo nome do espaço de trabalho, grupo de recursos, subscrição e região como no espaço de trabalho eliminado, o seu espaço de trabalho será recuperado, incluindo os seus dados, configuração e agentes conectados.
2. Se utilizar o mesmo nome do espaço de trabalho, mas diferente grupo de recursos, subscrição ou região, obterá um erro *O nome do espaço de trabalho 'workspace-name' não é único*, ou *conflito*. Para anular o soft-delete e eliminar permanentemente o seu espaço de trabalho e criar um novo espaço de trabalho com o mesmo nome, siga estes passos para recuperar primeiro o espaço de trabalho e realizar a eliminação permanente:
   * [Recuperar](../logs/delete-workspace.md#recover-workspace) a área de trabalho
   * [Eliminar de forma permanente](../logs/delete-workspace.md#permanent-workspace-delete) a área de trabalho
   * Criar um novo espaço de trabalho usando o mesmo nome do espaço de trabalho

## <a name="next-steps"></a>Passos seguintes
Agora que tem um espaço de trabalho disponível, pode configurar a recolha de telemetria de monitorização, executar pesquisas de registo para analisar esses dados e adicionar uma solução de gestão para fornecer dados adicionais e insights analíticos.  

* Para permitir a recolha de dados a partir de recursos Azure com Azure Diagnostics ou armazenamento Azure, consulte [registos e métricas de serviço Da Recolha Azure para utilização no Log Analytics](../essentials/resource-logs.md#send-to-log-analytics-workspace).  
* Adicione [o Gestor de Operações do System Center como uma fonte de dados](../agents/om-agents.md) para recolher dados de agentes que reportam o seu grupo de gestão de Gestor de Operações e armazene-os no seu espaço de trabalho Log Analytics.  
* Ligue [o Gestor de Configuração](../logs/collect-sccm.md) à importação de computadores que sejam membros de coleções na hierarquia.  
* Reveja as [soluções de monitorização](../insights/solutions.md) disponíveis e como adicionar ou remover uma solução do seu espaço de trabalho.

