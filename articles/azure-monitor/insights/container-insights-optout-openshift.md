---
title: Como parar de monitorar o cluster do Azure Red Hat OpenShift | Microsoft Docs
description: Este artigo descreve como você pode interromper o monitoramento do seu cluster do Azure Red Hat OpenShift com Azure Monitor para contêineres.
ms.topic: conceptual
ms.date: 11/21/2019
ms.openlocfilehash: e726d2d8254598869f1c6305421c674c870e3d31
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75404297"
---
# <a name="how-to-stop-monitoring-your-azure-red-hat-openshift-cluster-with-azure-monitor-for-containers"></a>Como parar de monitorar o cluster do Azure Red Hat OpenShift com Azure Monitor para contêineres

Depois de habilitar o monitoramento do cluster do Azure Red Hat OpenShift, você poderá parar de monitorar o cluster se decidir que não deseja mais monitorá-lo. Este artigo mostra como fazer isso usando os modelos de Azure Resource Manager fornecidos.  

## <a name="azure-resource-manager-template"></a>Modelo Azure Resource Manager

São fornecido dois modelo Azure Resource Manager para suportar a remoção os recursos de solução de forma consistente e repetida no seu grupo de recursos. Um é um modelo JSON que especifica a configuração para interromper o monitoramento e o outro contém valores de parâmetro que você configura para especificar a ID de recurso de cluster OpenShift e a região do Azure na qual o cluster é implantado. 

Se não estiver familiarizado com o conceito de implementar recursos com um modelo, consulte:
* [Implementar recursos com modelos do Resource Manager e o Azure PowerShell](../../azure-resource-manager/resource-group-template-deploy.md)
* [Implementar recursos com modelos do Resource Manager e a CLI do Azure](../../azure-resource-manager/resource-group-template-deploy-cli.md)

Se optar por utilizar a CLI do Azure, tem primeiro de instalar e utilizar a CLI localmente. Você deve estar executando o CLI do Azure versão 2.0.65 ou posterior. Para identificar a versão, execute `az --version`. Se precisar de instalar ou atualizar a CLI do Azure, veja [instalar a CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli). 

### <a name="create-template"></a>Criar o modelo

1. Copie e cole a seguinte sintaxe JSON no seu ficheiro:

    ```json
    {
       "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion": "1.0.0.0",
       "parameters": {
         "aroResourceId": {
           "type": "string",
           "metadata": {
             "description": "ARO Cluster Resource ID"
          }
        },
        "aroResourceLocation": {
          "type": "string",
          "metadata": {
            "description": "Location of the aro cluster resource e.g. westcentralus"
          }
        }
      },
      "resources": [
        {
           "name": "[split(parameters('aroResourceId'),'/')[8]]",
           "type": "Microsoft.ContainerService/openShiftManagedClusters",
           "location": "[parameters('aroResourceLocation')]",
           "apiVersion": "2019-09-30-preview",
           "properties": {
             "mode": "Incremental",
             "id": "[parameters('aroResourceId')]",
             "monitorProfile": {
               "workspaceResourceID": null,
               "enabled": false
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
        "aroResourceId": {
          "value": "/subscriptions/<subscriptionId>/resourceGroups/<ResourceGroupName>/providers/Microsoft.ContainerService/openShiftManagedClusters/<clusterName>"
        },
        "aroResourceLocation": {
          "value": "<azure region of the cluster e.g. westcentralus>"
        }
      }
    }
    ```

4. Edite os valores de **aroResourceId** e **aroResourceLocation** usando os valores do cluster OpenShift, que podem ser encontrados na página de **Propriedades** do cluster selecionado.

    ![Página de propriedades do contentor](media/container-insights-optout-openshift/cluster-properties-page.png)

5. Guarde este ficheiro como **OptOutParam.json** para uma pasta local.

6. Está pronto para implementar este modelo. 

### <a name="remove-the-solution-using-azure-cli"></a>Remover a solução com a CLI do Azure

Execute o comando a seguir com CLI do Azure no Linux para remover a solução e limpar a configuração no cluster.

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

Execute os seguintes comandos do PowerShell na pasta que contém o modelo para remover a solução e limpar a configuração do cluster.    

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

Se a área de trabalho foi criada apenas para suportar a monitorização do cluster e já não for necessário, terá de eliminar manualmente. Se você não estiver familiarizado com como excluir um espaço de trabalho, consulte [excluir um espaço de trabalho do Azure log Analytics](../../log-analytics/log-analytics-manage-del-workspace.md). 
