---
title: Como parar de monitorar o cluster do serviço kubernetes do Azure | Microsoft Docs
description: Este artigo descreve como você pode descontinuar o monitoramento do seu cluster AKS do Azure com Azure Monitor para contêineres.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 08/19/2019
ms.openlocfilehash: 508bfa9cf7bff0084e7f0644ee5e053e683cb9cf
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/17/2019
ms.locfileid: "72554111"
---
# <a name="how-to-stop-monitoring-your-azure-kubernetes-service-aks-with-azure-monitor-for-containers"></a>Como parar de monitorar o AKS (serviço kubernetes do Azure) com Azure Monitor para contêineres

Depois de habilitar o monitoramento do cluster AKS, você poderá parar de monitorar o cluster se decidir que não deseja mais monitorá-lo. Este artigo mostra como fazer isso usando o CLI do Azure ou com os modelos de Azure Resource Manager fornecidos.  


## <a name="azure-cli"></a>CLI do Azure

Use o comando [AZ AKs Disable-addons](https://docs.microsoft.com/cli/azure/aks?view=azure-cli-latest#az-aks-disable-addons) para desabilitar Azure monitor para contêineres. O comando Remove o agente dos nós de cluster, ele não remove a solução ou os dados já coletados e armazenados em seu recurso de Azure Monitor.  

```azurecli
az aks disable-addons -a monitoring -n MyExistingManagedCluster -g MyExistingManagedClusterRG
```

Para reabilitar o monitoramento do cluster, consulte [habilitar o monitoramento usando CLI do Azure](container-insights-enable-new-cluster.md#enable-using-azure-cli).

## <a name="azure-resource-manager-template"></a>Modelo Azure Resource Manager

São fornecidas duas Azure Resource Manager modelo para dar suporte à remoção de recursos da solução de forma consistente e repetida em seu grupo de recursos. Um é um modelo JSON que especifica a configuração para interromper o monitoramento e o outro contém valores de parâmetro que você configura para especificar a ID de recurso de cluster AKS e o grupo de recursos no qual o cluster é implantado. 

Se você não estiver familiarizado com o conceito de implantação de recursos usando um modelo, consulte:
* [Implementar recursos com modelos do Resource Manager e o Azure PowerShell](../../azure-resource-manager/resource-group-template-deploy.md)
* [Implantar recursos com modelos do Resource Manager e o CLI do Azure](../../azure-resource-manager/resource-group-template-deploy-cli.md)

>[!NOTE]
>O modelo precisa ser implantado no mesmo grupo de recursos do cluster. Se você omitir quaisquer outras propriedades ou Complementos ao usar esse modelo, isso poderá resultar na remoção do cluster. Por exemplo, *enableRBAC* para políticas RBAC implementadas em seu cluster ou *aksResourceTagValues* se as marcas forem especificadas para o cluster AKs.  
>

Se você optar por usar o CLI do Azure, primeiro será necessário instalar e usar a CLI localmente. Você deve estar executando o CLI do Azure versão 2.0.27 ou posterior. Para identificar sua versão, execute `az --version`. Se você precisar instalar ou atualizar o CLI do Azure, consulte [instalar o CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli). 

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

3. Cole a seguinte sintaxe JSON em seu arquivo:

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

    ![Página Propriedades do contêiner](media/container-insights-optout/container-properties-page.png)

    Enquanto você estiver na página **Propriedades** , copie também a **ID de recurso do espaço de trabalho**. Esse valor será necessário se você decidir que deseja excluir o espaço de trabalho Log Analytics mais tarde. Excluir o espaço de trabalho Log Analytics não é executado como parte desse processo. 

    Edite os valores de **aksResourceTagValues** para corresponder aos valores de marca existentes especificados para o cluster AKs.

5. Salve esse arquivo como **OptOutParam. JSON** em uma pasta local.

6. Está pronto para implementar este modelo. 

### <a name="remove-the-solution-using-azure-cli"></a>Remover a solução usando CLI do Azure

Execute o comando a seguir com CLI do Azure no Linux para remover a solução e limpar a configuração no cluster AKS.

```azurecli
az login   
az account set --subscription "Subscription Name" 
az group deployment create --resource-group <ResourceGroupName> --template-file ./OptOutTemplate.json --parameters @./OptOutParam.json  
```

A alteração de configuração pode levar alguns minutos para ser concluída. Quando for concluída, uma mensagem semelhante à seguinte que inclui o resultado será retornada:

```azurecli
ProvisioningState       : Succeeded
```

### <a name="remove-the-solution-using-powershell"></a>Remover a solução usando o PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Execute os seguintes comandos do PowerShell na pasta que contém o modelo para remover a solução e limpar a configuração do cluster AKS.    

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName <yourSubscriptionName>
New-AzResourceGroupDeployment -Name opt-out -ResourceGroupName <ResourceGroupName> -TemplateFile .\OptOutTemplate.json -TemplateParameterFile .\OptOutParam.json
```

A alteração de configuração pode levar alguns minutos para ser concluída. Quando for concluída, uma mensagem semelhante à seguinte que inclui o resultado será retornada:

```powershell
ProvisioningState       : Succeeded
```

Se o espaço de trabalho tiver sido criado apenas para dar suporte ao monitoramento do cluster e ele não for mais necessário, você precisará excluí-lo manualmente. Se você não estiver familiarizado com como excluir um espaço de trabalho, consulte [excluir um espaço de trabalho do Azure log Analytics com o portal do Azure](../../log-analytics/log-analytics-manage-del-workspace.md). Não se esqueça da **ID de recurso do espaço de trabalho** que copiamos anteriormente na etapa 4, você precisará dela. 

