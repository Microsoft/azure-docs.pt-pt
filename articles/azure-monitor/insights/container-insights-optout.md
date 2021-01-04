---
title: Como parar de monitorizar o seu cluster de serviçoS Azure Kubernetes Microsoft Docs
description: Este artigo descreve como pode descontinuar a monitorização do seu cluster Azure AKS com o Azure Monitor para contentores.
ms.topic: conceptual
ms.date: 08/19/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: 99c4ecb6c8b77f1576f25816ba486ed262249c32
ms.sourcegitcommit: b6267bc931ef1a4bd33d67ba76895e14b9d0c661
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/19/2020
ms.locfileid: "97695709"
---
# <a name="how-to-stop-monitoring-your-azure-kubernetes-service-aks-with-azure-monitor-for-containers"></a>Como parar a monitorização do Azure Kubernetes Service (AKS) com o Azure Monitor para contentores

Depois de ativar a monitorização do seu cluster AKS, pode parar de monitorizar o cluster se decidir que já não o quer monitorizar. Este artigo mostra como fazê-lo usando o CLI Azure ou com os modelos de Gestor de Recursos Azure fornecidos.  


## <a name="azure-cli"></a>CLI do Azure

Utilize o comando [az aks para desativar os addons](/cli/azure/aks?view=azure-cli-latest#az-aks-disable-addons) para desativar o Monitor Azure para recipientes. O comando remove o agente dos nós do cluster, não remove a solução ou os dados já recolhidos e armazenados no seu recurso Azure Monitor.  

```azurecli
az aks disable-addons -a monitoring -n MyExistingManagedCluster -g MyExistingManagedClusterRG
```

Para voltar a ativar a monitorização do seu cluster, consulte [Ativar a monitorização utilizando o Azure CLI](container-insights-enable-new-cluster.md#enable-using-azure-cli).

## <a name="azure-resource-manager-template"></a>Modelo Azure Resource Manager

Fornecido são dois modelos Azure Resource Manager para suportar a remoção dos recursos da solução de forma consistente e repetida no seu grupo de recursos. Um é um modelo JSON especificando a configuração para parar a monitorização e o outro contém valores de parâmetros que configura para especificar o ID de recursos do cluster AKS e o grupo de recursos em que o cluster é implantado.

Se não está familiarizado com o conceito de implantação de recursos usando um modelo, consulte:
* [Implementar recursos com modelos do Resource Manager e o Azure PowerShell](../../azure-resource-manager/templates/deploy-powershell.md)
* [Implementar recursos com modelos de Gestor de Recursos e o CLI Azure](../../azure-resource-manager/templates/deploy-cli.md)

>[!NOTE]
>O modelo precisa de ser implantado no mesmo grupo de recursos do cluster. Se omitir quaisquer outras propriedades ou addons ao utilizar este modelo, pode resultar na sua remoção do cluster. Por exemplo, ativar as políticas *DECCC* de Kubernetes implementadas no seu cluster, ou *aksResourceTagValues* se as tags forem especificadas para o cluster AKS.  
>

Se optar por utilizar o Azure CLI, primeiro tem de instalar e utilizar o CLI localmente. Deve estar a executar a versão Azure CLI 2.0.27 ou posterior. Para identificar a sua versão, corra `az --version` . Se necessitar de instalar ou atualizar o Azure CLI, consulte [instalar o Azure CLI](/cli/azure/install-azure-cli).

### <a name="create-template"></a>Criar o modelo

1. Copie e cole a seguinte sintaxe JSON no seu ficheiro:

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "aksResourceId": {
           "type": "string",
           "metadata": {
             "description": "AKS Cluster Resource ID"
           }
       },
      "aksResourceLocation": {
        "type": "string",
        "metadata": {
           "description": "Location of the AKS resource e.g. \"East US\""
         }
       },
    "aksResourceTagValues": {
      "type": "object",
      "metadata": {
        "description": "Existing all tags on AKS Cluster Resource"
        }
      }
     },
    "resources": [
      {
        "name": "[split(parameters('aksResourceId'),'/')[8]]",
        "type": "Microsoft.ContainerService/managedClusters",
        "location": "[parameters('aksResourceLocation')]",
        "tags": "[parameters('aksResourceTagValues')]",
        "apiVersion": "2018-03-31",
        "properties": {
          "mode": "Incremental",
          "id": "[parameters('aksResourceId')]",
          "addonProfiles": {
            "omsagent": {
              "enabled": false,
              "config": null
            }
           }
         }
       }
      ]
    }
    ```

2. Guarde este ficheiro à medida **queOptOutTemplate.jsnuma** pasta local.

3. Cole a seguinte sintaxe JSON no seu ficheiro:

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "aksResourceId": {
          "value": "/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroup>/providers/Microsoft.ContainerService/managedClusters/<ResourceName>"
        },
        "aksResourceLocation": {
          "value": "<aksClusterRegion>"
        },
        "aksResourceTagValues": {
          "value": {
            "<existing-tag-name1>": "<existing-tag-value1>",
            "<existing-tag-name2>": "<existing-tag-value2>",
            "<existing-tag-nameN>": "<existing-tag-valueN>"
          }
        }
      }
    }
    ```

4. Editar os valores para **aksResourceId** e **aksResourceLocation** utilizando os valores do cluster AKS, que pode encontrar na página **Propriedades** para o cluster selecionado.

    ![Página de propriedades de contentores](media/container-insights-optout/container-properties-page.png)

    Enquanto estiver na página **Propriedades,** também copie o **ID do Recurso workspace.** Este valor é necessário se decidir que pretende eliminar o espaço de trabalho Do Log Analytics mais tarde. A eliminação do espaço de trabalho Log Analytics não é realizada como parte deste processo.

    Editar os **valores para aksResourceTagValues** para corresponder aos valores de etiqueta existentes especificados para o cluster AKS.

5. Guarde este ficheiro à medida **queOptOutParam.jsnuma** pasta local.

6. Está pronto para implementar este modelo.

### <a name="remove-the-solution-using-azure-cli"></a>Remova a solução utilizando o Azure CLI

Execute o seguinte comando com O Azure CLI no Linux para remover a solução e limpar a configuração no seu cluster AKS.

```azurecli
az login   
az account set --subscription "Subscription Name"
az deployment group create --resource-group <ResourceGroupName> --template-file ./OptOutTemplate.json --parameters @./OptOutParam.json  
```

A alteração de configuração pode demorar alguns minutos a ser concluída. Quando estiver concluída, uma mensagem semelhante à seguinte que inclui o resultado é devolvida:

```output
ProvisioningState       : Succeeded
```

### <a name="remove-the-solution-using-powershell"></a>Remova a solução utilizando o PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Execute os seguintes comandos PowerShell na pasta que contém o gabarito para remover a solução e limpar a configuração do seu cluster AKS.    

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName <yourSubscriptionName>
New-AzResourceGroupDeployment -Name opt-out -ResourceGroupName <ResourceGroupName> -TemplateFile .\OptOutTemplate.json -TemplateParameterFile .\OptOutParam.json
```

A alteração de configuração pode demorar alguns minutos a ser concluída. Quando estiver concluída, uma mensagem semelhante à seguinte que inclui o resultado é devolvida:

```output
ProvisioningState       : Succeeded
```


## <a name="next-steps"></a>Passos seguintes

Se o espaço de trabalho foi criado apenas para suportar a monitorização do cluster e já não é necessário, tem de o eliminar manualmente. Se não estiver familiarizado com a forma de eliminar um espaço de trabalho, consulte [Eliminar um espaço de trabalho Azure Log Analytics com o portal Azure](../platform/delete-workspace.md). Não te esqueças do **ID do Espaço de Trabalho** copiado mais cedo no passo 4, vais precisar disso.
