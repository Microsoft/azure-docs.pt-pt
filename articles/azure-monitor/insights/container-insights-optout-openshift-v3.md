---
title: Como parar de monitorizar o seu cluster Azure Red Hat OpenShift v3 [ Microsoft Docs
description: Este artigo descreve como pode parar de monitorizar o seu cluster Azure Red Hat OpenShift com o Monitor Azure para contentores.
ms.topic: conceptual
ms.date: 04/24/2020
ms.openlocfilehash: e1d3200af62ad185fa942fa2c8f7f3b4e6bfd89b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82196195"
---
# <a name="how-to-stop-monitoring-your-azure-red-hat-openshift-v3-cluster"></a>Como parar de monitorizar o seu cluster OpenShift v3 do seu chapéu vermelho Azure

Depois de ativar a monitorização do seu cluster Azure Red Hat OpenShift 3.x, pode parar de monitorizar o cluster com o Monitor Azure para obter contentores se decidir que já não o pretende monitorizar. Este artigo mostra como fazê-lo usando o modelo de Gestor de Recursos Azure fornecido.  

## <a name="azure-resource-manager-template"></a>Modelo Azure Resource Manager

São fornecidos dois modelos do Gestor de Recursos Azure para apoiar a remoção dos recursos de solução de forma consistente e repetida no seu grupo de recursos. Um é um modelo JSON que especifica a configuração para parar de monitorizar e o outro contém valores de parâmetros que configura para especificar o ID de dispositivo de cluster OpenShift e a região azure em que o cluster está implantado.

Se não está familiarizado com o conceito de implantação de recursos usando um modelo, consulte:
* [Implementar recursos com modelos do Resource Manager e do Azure PowerShell](../../azure-resource-manager/templates/deploy-powershell.md)
* [Implementar recursos com modelos de Gestor de Recursos e o Azure CLI](../../azure-resource-manager/templates/deploy-cli.md)

Se optar por utilizar o Azure CLI, primeiro necessita de instalar e utilizar o CLI localmente. Deve estar a executar a versão Azure CLI 2.0.65 ou mais tarde. Para identificar a `az --version`sua versão, corra. Se precisar de instalar ou atualizar o Azure CLI, consulte [Instalar o Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

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

3. Colhe a seguinte sintaxe JSON no seu ficheiro:

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

5. Guarde este ficheiro como **OptOutParam.json** para uma pasta local.

6. Está pronto para implementar este modelo.

### <a name="remove-the-solution-using-azure-cli"></a>Remova a solução utilizando o Azure CLI

Execute o seguinte comando com o Azure CLI no Linux para remover a solução e limpar a configuração do seu cluster.

```azurecli
az login   
az account set --subscription "Subscription Name"
az group deployment create --resource-group <ResourceGroupName> --template-file ./OptOutTemplate.json --parameters @./OptOutParam.json  
```

A mudança de configuração pode demorar alguns minutos a ser completada. Quando estiver concluída, uma mensagem semelhante à seguinte que inclui o resultado é devolvida:

```output
ProvisioningState       : Succeeded
```

### <a name="remove-the-solution-using-powershell"></a>Remova a solução utilizando powerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Execute os seguintes comandos PowerShell na pasta que contém o modelo para remover a solução e limpar a configuração do seu cluster.    

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName <yourSubscriptionName>
New-AzResourceGroupDeployment -Name opt-out -ResourceGroupName <ResourceGroupName> -TemplateFile .\OptOutTemplate.json -TemplateParameterFile .\OptOutParam.json
```

A mudança de configuração pode demorar alguns minutos a ser completada. Quando estiver concluída, uma mensagem semelhante à seguinte que inclui o resultado é devolvida:

```output
ProvisioningState       : Succeeded
```

## <a name="next-steps"></a>Passos seguintes

Se o espaço de trabalho foi criado apenas para suportar a monitorização do cluster e já não for necessário, tem de o apagar manualmente. Se não estiver familiarizado com a forma de eliminar um espaço de trabalho, consulte [Delete a Azure Log Analytics workspace](../../log-analytics/log-analytics-manage-del-workspace.md).
