---
title: Como parar monitorização do seu cluster do Azure Kubernetes Service | Documentos da Microsoft
description: Este artigo descreve como interromper a monitorização do seu cluster do AKS do Azure com o Azure Monitor para contentores.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/13/2018
ms.author: magoedte
ms.openlocfilehash: 16abcd2130e92f182dc129c2d7f5cd07ac04a766
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2019
ms.locfileid: "57313536"
---
# <a name="how-to-stop-monitoring-your-azure-kubernetes-service-aks-with-azure-monitor-for-containers"></a>Como parar a monitorização do Azure Kubernetes Service (AKS) com o Azure Monitor para contentores

Depois de ativar a monitorização do seu cluster do AKS, pode parar a monitorização do cluster, se decidir que já não pretende monitorizá-lo. Este artigo mostra como efetuar este procedimento utilizando a CLI do Azure ou com modelos do Azure Resource Manager fornecidos.  


## <a name="azure-cli"></a>CLI do Azure
Utilize o [az aks disable-complementos](https://docs.microsoft.com/cli/azure/aks?view=azure-cli-latest#az-aks-disable-addons) comando para desativar o Monitor do Azure para contentores. O comando remove o agente de nós do cluster, não remove a solução ou os dados já recolhidos e armazenados no seu recurso do Azure Monitor.  

```azurecli
az aks disable-addons -a monitoring -n MyExistingManagedCluster -g MyExistingManagedClusterRG
```

Para voltar a Ativar monitorização do seu cluster, veja [ativar a monitorização com a CLI do Azure](container-insights-onboard.md#enable-monitoring-using-azure-cli).

## <a name="azure-resource-manager-template"></a>Modelo Azure Resource Manager
São fornecido dois modelo Azure Resource Manager para suportar a remoção os recursos de solução de forma consistente e repetida no seu grupo de recursos. Uma é um modelo JSON especificar a configuração para parar a monitorização e a outra contém valores de parâmetros que pode configurar para especificar o grupo de recurso e o ID de recurso de cluster para AKS que o cluster é implementado no. 

Se não estiver familiarizado com o conceito de implementar recursos com um modelo, consulte:
* [Implementar recursos com modelos do Resource Manager e o Azure PowerShell](../../azure-resource-manager/resource-group-template-deploy.md)
* [Implementar recursos com modelos do Resource Manager e a CLI do Azure](../../azure-resource-manager/resource-group-template-deploy-cli.md)

>[!NOTE]
>O modelo precisa ser implantado no mesmo grupo de recursos do cluster. Se omitir quaisquer outras propriedades ou suplementos ao utilizar este modelo, pode resultar na respetiva remoção do cluster. Por exemplo, *enableRBAC*.  
>

Se optar por utilizar a CLI do Azure, tem primeiro de instalar e utilizar a CLI localmente. Tem de executar a CLI do Azure versão 2.0.27 ou posterior. Para identificar a versão, execute `az --version`. Se precisar de instalar ou atualizar a CLI do Azure, veja [instalar a CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli). 

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
    "resources": [
      {
        "name": "[split(parameters('aksResourceId'),'/')[8]]",
        "type": "Microsoft.ContainerService/managedClusters",
        "location": "[parameters('aksResourceLocation')]",
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
        }
      }
    }
    ```

4. Edite os valores dos **aksResourceId** e **aksResourceLocation** utilizando os valores do cluster do AKS, que pode ser encontrado no **propriedades** página para o cluster selecionado .

    ![Página de propriedades do contentor](media/container-insights-optout/container-properties-page.png)

    Enquanto estiver a utilizar o **propriedades** página, também copiar a **ID de recurso da área de trabalho**. Este valor é necessário se decidir que pretende eliminar a área de trabalho do Log Analytics mais tarde. A eliminar a área de trabalho do Log Analytics não é efetuada como parte deste processo. 

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

Se a área de trabalho foi criada apenas para suportar a monitorização do cluster e já não for necessário, terá de eliminar manualmente. Se não estiver familiarizado com como eliminar uma área de trabalho, consulte [eliminar uma área de trabalho do Log Analytics do Azure com o portal do Azure](../../log-analytics/log-analytics-manage-del-workspace.md). Não se esqueça da **ID de recurso da área de trabalho** copiou anteriormente no passo 4, vai precisar que. 

