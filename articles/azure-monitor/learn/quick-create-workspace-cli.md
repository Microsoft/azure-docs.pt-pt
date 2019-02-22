---
title: Criar uma área de trabalho do Log Analytics com a CLI do Azure | Documentos da Microsoft
description: Saiba como criar uma área de trabalho do Log Analytics para ativar a recolha de dados e soluções de gerenciamento de seus ambientes na cloud e no local com a CLI do Azure.
services: log-analytics
documentationcenter: log-analytics
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/21/2019
ms.author: magoedte
ms.openlocfilehash: 76ddbe34650e72b12344f78bc74280f114e5d26c
ms.sourcegitcommit: a8948ddcbaaa22bccbb6f187b20720eba7a17edc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/21/2019
ms.locfileid: "56592439"
---
# <a name="create-a-log-analytics-workspace-with-azure-cli-20"></a>Criar uma área de trabalho do Log Analytics com a CLI 2.0 do Azure

A CLI 2.0 do Azure é utilizada para criar e gerir recursos do Azure a partir da linha de comandos ou em scripts. Este guia de introdução mostra-lhe como utilizar a CLI 2.0 do Azure para implementar uma área de trabalho do Log Analytics no Azure Monitor. Uma área de trabalho do Log Analytics é um ambiente exclusivo para dados de registo do Azure Monitor. Cada área de trabalho tem seu próprio repositório de dados e a configuração e origens de dados e as soluções são configuradas para armazenar seus dados numa área de trabalho específica. Exigir uma área de trabalho do Log Analytics se pretende recolher dados das seguintes origens:

* Recursos do Azure na sua subscrição  
* Computadores monitorizados pelo System Center Operations Manager no local  
* Coleções de dispositivos do System Center Configuration Manager  
* Dados de diagnóstico ou de registo do armazenamento do Azure  
 
Para outras origens, como as VMs do Azure e o Windows ou VMs do Linux no seu ambiente, consulte os seguintes tópicos:

* [Recolher dados de máquinas virtuais do Azure](../learn/quick-collect-azurevm.md)
* [Recolher dados de computador com Linux híbrida](../learn/quick-collect-linux-computer.md)
* [Recolher dados do computador de Windows híbrida](quick-collect-windows-computer.md)

Se não tiver uma subscrição do Azure, crie [uma conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar a CLI localmente, este guia de início rápido requer a execução da versão 2.0.30 ou posterior da CLI do Azure. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [instalar a CLI 2.0 do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="create-a-workspace"></a>Criar uma área de trabalho
Criar uma área de trabalho com [criar a implementação do grupo az](https://docs.microsoft.com/cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-create). O exemplo seguinte cria uma área de trabalho com o nome *TestWorkspace* no grupo de recursos *laboratório* no *eastus* local usando um modelo do Resource Manager do local máquina. O modelo JSON está configurado para apenas solicitar-lhe o nome da área de trabalho e especifica um valor predefinido para os outros parâmetros que provavelmente seria usado como uma configuração padrão no seu ambiente. Ou pode armazenar o modelo numa conta de armazenamento do Azure para acesso partilhado na sua organização. Para obter mais informações sobre como trabalhar com modelos, consulte [implementar recursos com modelos do Resource Manager e a CLI do Azure](../../azure-resource-manager/resource-group-template-deploy-cli.md)

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

2. Edite o modelo para satisfazer os seus requisitos.  Revisão [Microsoft.OperationalInsights/workspaces modelo](https://docs.microsoft.com/azure/templates/microsoft.operationalinsights/workspaces) referência para saber quais propriedades e valores são suportados. 
3. Guarde este ficheiro como **deploylaworkspacetemplate.json** para uma pasta local.   
4. Está pronto para implementar este modelo. Utilize os seguintes comandos a partir da pasta que contém o modelo:

    ```azurecli
    az group deployment create --resource-group <my-resource-group> --name <my-deployment-name> --template-file deploylaworkspacetemplate.json
    ```

A implementação pode demorar alguns minutos a concluir. Quando terminar, verá uma mensagem semelhante ao seguinte, que inclui o resultado:

![Exemplo de resultado quando a implementação estiver concluída](media/quick-create-workspace-cli/template-output-01.png)

## <a name="next-steps"></a>Passos Seguintes
Agora que tem uma área de trabalho disponível, pode configurar a recolha de monitorização de telemetria, executar pesquisas de registos para analisar esses dados e adicionar uma solução de gestão para fornecer dados adicionais e informações de análise.  

* Para ativar a recolha de dados dos recursos do Azure com o diagnóstico do Azure ou o armazenamento do Azure, veja [registos do serviço de recolha do Azure e as métricas de utilização do Log Analytics](../platform/collect-azure-metrics-logs.md).  
* Adicione [System Center Operations Manager como origem de dados](../platform/om-agents.md) para recolher dados de agentes que reportam o grupo de gestão do Operations Manager e o armazenamos em sua área de trabalho do Log Analytics.  
* Ligue-se [Configuration Manager](../platform/collect-sccm.md) para importar computadores que são membros de coleções na hierarquia.  
* Reveja os [soluções de monitorização](../insights/solutions.md) disponíveis e como adicionar ou remover uma solução da sua área de trabalho.
