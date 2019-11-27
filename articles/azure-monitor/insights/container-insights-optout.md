---
title: Como parar monitorização do seu cluster do Azure Kubernetes Service | Documentos da Microsoft
description: Este artigo descreve como interromper a monitorização do seu cluster do AKS do Azure com o Azure Monitor para contentores.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 08/19/2019
ms.openlocfilehash: fe0155d6102dac12d5d4c01b78b1ddd45f9bee02
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74382235"
---
# <a name="how-to-stop-monitoring-your-azure-kubernetes-service-aks-with-azure-monitor-for-containers"></a>Como parar a monitorização do Azure Kubernetes Service (AKS) com o Azure Monitor para contentores

Depois de ativar a monitorização do seu cluster do AKS, pode parar a monitorização do cluster, se decidir que já não pretende monitorizá-lo. Este artigo mostra como efetuar este procedimento utilizando a CLI do Azure ou com modelos do Azure Resource Manager fornecidos.  


## <a name="azure-cli"></a>CLI do Azure

Use o comando [AZ AKs Disable-addons](https://docs.microsoft.com/cli/azure/aks?view=azure-cli-latest#az-aks-disable-addons) para desabilitar Azure monitor para contêineres. O comando Remove o agente dos nós de cluster, ele não remove a solução ou os dados já coletados e armazenados em seu recurso de Azure Monitor.  

```azurecli
az aks disable-addons -a monitoring -n MyExistingManagedCluster -g MyExistingManagedClusterRG
```

Para reabilitar o monitoramento do cluster, consulte [habilitar o monitoramento usando CLI do Azure](container-insights-enable-new-cluster.md#enable-using-azure-cli).

## <a name="azure-resource-manager-template"></a>Modelo Azure Resource Manager

São fornecido dois modelo Azure Resource Manager para suportar a remoção os recursos de solução de forma consistente e repetida no seu grupo de recursos. Um é um modelo JSON que especifica a configuração para interromper o monitoramento e o outro contém valores de parâmetro que você configura para especificar a ID de recurso de cluster AKS e o grupo de recursos no qual o cluster é implantado. 

Se não estiver familiarizado com o conceito de implementar recursos com um modelo, consulte:
* [Implementar recursos com modelos do Resource Manager e o Azure PowerShell](../../azure-resource-manager/resource-group-template-deploy.md)
* [Implantar recursos com modelos do Resource Manager e o CLI do Azure](../../azure-resource-manager/resource-group-template-deploy-cli.md)

>[!NOTE]
>O modelo precisa ser implantado no mesmo grupo de recursos do cluster. Se você omitir quaisquer outras propriedades ou Complementos ao usar esse modelo, isso poderá resultar na remoção do cluster. Por exemplo, *enableRBAC* para políticas RBAC implementadas em seu cluster ou *aksResourceTagValues* se as marcas forem especificadas para o cluster AKs.  
>

Se optar por utilizar a CLI do Azure, tem primeiro de instalar e utilizar a CLI localmente. Tem de executar a CLI do Azure versão 2.0.27 ou posterior. Para identificar sua versão, execute `az --version`. Se você precisar instalar ou atualizar o CLI do Azure, consulte [instalar o CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli). 

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
       }
       },
    "aksResourceTagValues": {
      "type": "object",
      "metadata": {
        "description": "Existing all tags on AKS Cluster Resource"
      }
    },
    "resources": [
      {
        "name": "[split(parameters('aksResourceId'),'/')[8]]",
        "type": "Microsoft.ContainerService/managedClusters",
        "location": "[parameters('aksResourceLocation')]",
        "tags": "[parameters('aksResourceTagValues')]"
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

2. Salve esse arquivo como **OptOutTemplate. JSON** em uma pasta local.

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

4. Edite os valores de **aksResourceId** e **aksResourceLocation** usando os valores do cluster AKs, que podem ser encontrados na página de **Propriedades** do cluster selecionado.

    ![Página de propriedades do contentor](media/container-insights-optout/container-properties-page.png)

    Enquanto você estiver na página **Propriedades** , copie também a **ID de recurso do espaço de trabalho**. Este valor é necessário se decidir que pretende eliminar a área de trabalho do Log Analytics mais tarde. A eliminar a área de trabalho do Log Analytics não é efetuada como parte deste processo. 

    Edite os valores de **aksResourceTagValues** para corresponder aos valores de marca existentes especificados para o cluster AKs.

5. Salve esse arquivo como **OptOutParam. JSON** em uma pasta local.

6. Está pronto para implementar este modelo. 

### <a name="remove-the-solution-using-azure-cli"></a>Remover a solução com a CLI do Azure

Execute o seguinte comando com a CLI do Azure no Linux para remover a solução e limpar a configuração do seu cluster do AKS.

```azurecli
az login   
az account set --subscription "Subscription Name" 
az group deployment create --resource-group <ResourceGroupName> --template-file ./OptOutTemplate.json --parameters @./OptOutParam.json  
```

A alteração de configuração pode demorar alguns minutos a concluir. Quando for concluído, é devolvida uma mensagem semelhante ao seguinte, que inclui o resultado:

```azurecli
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

```powershell
ProvisioningState       : Succeeded
```


## <a name="next-steps"></a>Passos seguintes

Se a área de trabalho foi criada apenas para suportar a monitorização do cluster e já não for necessário, terá de eliminar manualmente. Se você não estiver familiarizado com como excluir um espaço de trabalho, consulte [excluir um espaço de trabalho do Azure log Analytics com o portal do Azure](../../log-analytics/log-analytics-manage-del-workspace.md). Não se esqueça de que a **ID de recurso do espaço de trabalho** foi copiada anteriormente na etapa 4, você precisará dela. 

