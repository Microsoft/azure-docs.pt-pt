---
title: Como parar de monitorizar o seu azure Red Hat OpenShift v3 cluster Microsoft Docs
description: Este artigo descreve como pode parar de monitorizar o seu cluster Azure Red Hat OpenShift com o Azure Monitor para contentores.
ms.topic: conceptual
ms.date: 04/24/2020
ms.openlocfilehash: 6ed92cd4cda1f3b5d43cc605d7224236528b94bf
ms.sourcegitcommit: d39f2cd3e0b917b351046112ef1b8dc240a47a4f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/25/2020
ms.locfileid: "88815441"
---
# <a name="how-to-stop-monitoring-your-azure-red-hat-openshift-v3-cluster"></a>Como parar de monitorizar o seu cluster Azure Red Hat OpenShift v3 

Depois de ativar a monitorização do seu cluster 3.x do Chapéu Vermelho Azure, pode parar de monitorizar o cluster com o Azure Monitor para contentores se decidir que já não o quer monitorizar. Este artigo mostra como fazê-lo usando o modelo Azure Resource Manager fornecido. 

## <a name="azure-resource-manager-template"></a>Modelo Azure Resource Manager

Fornecido são dois modelos Azure Resource Manager para suportar a remoção dos recursos da solução de forma consistente e repetida no seu grupo de recursos. Um é um modelo JSON especificando a configuração para parar a monitorização e o outro contém valores de parâmetros que configura para especificar o ID de recursos de cluster OpenShift e a região de Azure em que o cluster é implantado.

Se não está familiarizado com o conceito de implantação de recursos usando um modelo, consulte:
* [Implementar recursos com modelos do Resource Manager e o Azure PowerShell](../../azure-resource-manager/templates/deploy-powershell.md)
* [Implementar recursos com modelos de Gestor de Recursos e o CLI Azure](../../azure-resource-manager/templates/deploy-cli.md)

Se optar por utilizar o Azure CLI, primeiro tem de instalar e utilizar o CLI localmente. Deve estar a executar a versão Azure CLI 2.0.65 ou posterior. Para identificar a sua versão, corra `az --version` . Se necessitar de instalar ou atualizar o Azure CLI, consulte [instalar o Azure CLI](/cli/azure/install-azure-cli).

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

2. Guarde este ficheiro à medida ** queOptOutTemplate.jsnuma** pasta local.

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

4. Editar os valores para **aroResourceId** e **aroResourceLocation** utilizando os valores do cluster OpenShift, que pode encontrar na página **Propriedades** para o cluster selecionado.

    ![Página de propriedades de contentores](media/container-insights-optout-openshift/cluster-properties-page.png)

5. Guarde este ficheiro à medida ** queOptOutParam.jsnuma** pasta local.

6. Está pronto para implementar este modelo.

### <a name="remove-the-solution-using-azure-cli"></a>Remova a solução utilizando o Azure CLI

Execute o seguinte comando com O Azure CLI no Linux para remover a solução e limpar a configuração do seu cluster.

```azurecli
az login   
az account set --subscription "Subscription Name"
az group deployment create --resource-group <ResourceGroupName> --template-file ./OptOutTemplate.json --parameters @./OptOutParam.json  
```

A alteração de configuração pode demorar alguns minutos a ser concluída. Quando estiver concluída, uma mensagem semelhante à seguinte que inclui o resultado é devolvida:

```output
ProvisioningState       : Succeeded
```

### <a name="remove-the-solution-using-powershell"></a>Remova a solução utilizando o PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Execute os seguintes comandos PowerShell na pasta que contém o gabarito para remover a solução e limpar a configuração do seu cluster.    

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

Se o espaço de trabalho foi criado apenas para suportar a monitorização do cluster e já não é necessário, tem de o eliminar manualmente. Se não estiver familiarizado com a forma de eliminar um espaço de trabalho, consulte [Eliminar um espaço de trabalho Azure Log Analytics](../platform/delete-workspace.md).
