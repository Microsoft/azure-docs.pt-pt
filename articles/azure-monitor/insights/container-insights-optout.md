---
title: Como parar monitorização do seu cluster do Azure Kubernetes Service | Documentos da Microsoft
description: Este artigo descreve como interromper a monitorização do seu cluster do AKS do Azure com o Azure Monitor para contentores.
ms.topic: conceptual
ms.date: 08/19/2019
ms.openlocfilehash: 618a4d7e10212dd2b042724b1ea11c97920dad57
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78385456"
---
# <a name="how-to-stop-monitoring-your-azure-kubernetes-service-aks-with-azure-monitor-for-containers"></a>Como parar a monitorização do Azure Kubernetes Service (AKS) com o Azure Monitor para contentores

Depois de ativar a monitorização do seu cluster do AKS, pode parar a monitorização do cluster, se decidir que já não pretende monitorizá-lo. Este artigo mostra como efetuar este procedimento utilizando a CLI do Azure ou com modelos do Azure Resource Manager fornecidos.  


## <a name="azure-cli"></a>CLI do Azure

Utilize o comando [az aks de saque de saque](https://docs.microsoft.com/cli/azure/aks?view=azure-cli-latest#az-aks-disable-addons) para desativar o Monitor Azure para os recipientes. O comando remove o agente dos nós do cluster, não remove a solução ou os dados já recolhidos e armazenados no seu recurso Azure Monitor.  

```azurecli
az aks disable-addons -a monitoring -n MyExistingManagedCluster -g MyExistingManagedClusterRG
```

Para reativar a monitorização do seu cluster, consulte a [monitorização ativa utilizando o Azure CLI](container-insights-enable-new-cluster.md#enable-using-azure-cli).

## <a name="azure-resource-manager-template"></a>Modelo Azure Resource Manager

São fornecido dois modelo Azure Resource Manager para suportar a remoção os recursos de solução de forma consistente e repetida no seu grupo de recursos. Um é um modelo JSON especificando a configuração para parar de monitorizar e o outro contém valores de parâmetros que configura para especificar o ID de recurso de cluster AKS e o grupo de recursos em que o cluster está implantado.

Se não estiver familiarizado com o conceito de implementar recursos com um modelo, consulte:
* [Implementar recursos com modelos do Resource Manager e o Azure PowerShell](../../azure-resource-manager/templates/deploy-powershell.md)
* [Implementar recursos com modelos de Gestor de Recursos e o Azure CLI](../../azure-resource-manager/templates/deploy-cli.md)

>[!NOTE]
>O modelo precisa de ser implantado no mesmo grupo de recursos do cluster. Se omitir quaisquer outras propriedades ou addons ao utilizar este modelo, pode resultar na sua remoção do cluster. Por exemplo, *o enableRBAC* para as políticas RBAC implementadas no seu cluster, ou *aksResourceTagValues* se as etiquetas forem especificadas para o cluster AKS.  
>

Se optar por utilizar a CLI do Azure, tem primeiro de instalar e utilizar a CLI localmente. Tem de executar a CLI do Azure versão 2.0.27 ou posterior. Para identificar a sua versão, execute `az --version`. Se precisar de instalar ou atualizar o Azure CLI, consulte [Instalar o Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

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

2. Guarde este ficheiro como **OptOutTemplate.json** para uma pasta local.

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

4. Editar os **valores para aksResourceId** e **aksResourceLocation** utilizando os valores do cluster AKS, que pode encontrar na página **Propriedades** para o cluster selecionado.

    ![Página de propriedades do contentor](media/container-insights-optout/container-properties-page.png)

    Enquanto estiver na página **Propriedades,** também copie o ID do **Recurso Workspace**. Este valor é necessário se decidir que pretende eliminar a área de trabalho do Log Analytics mais tarde. A eliminar a área de trabalho do Log Analytics não é efetuada como parte deste processo.

    Editar os **valores para aksResourceTagValues** para corresponder aos valores de etiqueta existentes especificados para o cluster AKS.

5. Guarde este ficheiro como **OptOutParam.json** para uma pasta local.

6. Está pronto para implementar este modelo.

### <a name="remove-the-solution-using-azure-cli"></a>Remover a solução com a CLI do Azure

Execute o seguinte comando com a CLI do Azure no Linux para remover a solução e limpar a configuração do seu cluster do AKS.

```azurecli
az login   
az account set --subscription "Subscription Name"
az group deployment create --resource-group <ResourceGroupName> --template-file ./OptOutTemplate.json --parameters @./OptOutParam.json  
```

A alteração de configuração pode demorar alguns minutos a concluir. Quando for concluído, é devolvida uma mensagem semelhante ao seguinte, que inclui o resultado:

```output
ProvisioningState       : Succeeded
```

### <a name="remove-the-solution-using-powershell"></a>Remover a solução com o PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Execute os seguintes comandos do PowerShell na pasta que contém o modelo para remover a solução e limpar a configuração do seu cluster do AKS.    

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName <yourSubscriptionName>
New-AzResourceGroupDeployment -Name opt-out -ResourceGroupName <ResourceGroupName> -TemplateFile .\OptOutTemplate.json -TemplateParameterFile .\OptOutParam.json
```

A alteração de configuração pode demorar alguns minutos a concluir. Quando for concluído, é devolvida uma mensagem semelhante ao seguinte, que inclui o resultado:

```output
ProvisioningState       : Succeeded
```


## <a name="next-steps"></a>Passos seguintes

Se a área de trabalho foi criada apenas para suportar a monitorização do cluster e já não for necessário, terá de eliminar manualmente. Se não estiver familiarizado com a forma de eliminar um espaço de trabalho, consulte [Delete a Azure Log Analytics espaço de trabalho com o portal Azure](../../log-analytics/log-analytics-manage-del-workspace.md). Não te esqueças do ID do **Workspace Resource** copiado no primeiro passo 4, vais precisar disso.
