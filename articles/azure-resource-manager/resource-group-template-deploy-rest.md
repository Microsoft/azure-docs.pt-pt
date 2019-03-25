---
title: Implementar recursos com a REST API e o modelo | Documentos da Microsoft
description: Utilize o Azure Resource Manager e API de REST do Resource Manager para implementar recursos no Azure. Os recursos são definidos num modelo do Resource Manager.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
ms.assetid: 1d8fbd4c-78b0-425b-ba76-f2b7fd260b45
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/22/2019
ms.author: tomfitz
ms.openlocfilehash: 3468f5b625911cd637b22e2c1d35a47fb7d7b0e4
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/25/2019
ms.locfileid: "58402835"
---
# <a name="deploy-resources-with-resource-manager-templates-and-resource-manager-rest-api"></a>Implementar recursos com modelos do Resource Manager e API REST do Resource Manager

Este artigo explica como utilizar a API de REST do Resource Manager com modelos do Resource Manager para implementar os seus recursos no Azure.  

Pode optar por incluir o modelo no corpo do pedido ou ligação para um ficheiro. Quando utilizar um ficheiro, pode ser um ficheiro local ou um arquivo externo que está disponível através de um URI. Quando o modelo estiver numa conta de armazenamento, pode restringir o acesso ao modelo e fornecer um token de assinatura (SAS) de acesso partilhado durante a implementação.

## <a name="deployment-scope"></a>Escopo da implantação

Pode direcionar a sua implementação para uma subscrição do Azure ou um grupo de recursos numa subscrição. Na maioria dos casos, será o direcionamento de implementação para um grupo de recursos. Utilize implementações de subscrição para aplicar políticas e as atribuições de funções a subscrição. Também utilizar implementações de subscrição para criar um grupo de recursos e implementar recursos no mesmo. Dependendo do escopo da implantação, é usar comandos diferentes.

Para implementar um **grupo de recursos**, utilize [criar implementações -](/rest/api/resources/deployments/createorupdate).

Para implementar um **subscrição**, utilize [implementações - criar no âmbito da subscrição](/rest/api/resources/deployments/createorupdateatsubscriptionscope).

Os exemplos neste artigo utilizam implementações do grupo de recursos. Para obter mais informações sobre implementações de subscrição, veja [criar grupos de recursos e recursos ao nível da subscrição](deploy-to-subscription.md).

## <a name="deploy-with-the-rest-api"></a>Implementar com a API REST
1. Definir [parâmetros comuns e os cabeçalhos](/rest/api/azure/), incluindo os tokens de autenticação.

1. Se não tiver um grupo de recursos existente, crie um grupo de recursos. Forneça o ID da subscrição, o nome do novo grupo de recursos e localização que precisa para a sua solução. Para obter mais informações, consulte [criar um grupo de recursos](/rest/api/resources/resourcegroups/createorupdate).

   ```HTTP
   PUT https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>?api-version=2018-05-01
   ```

   Com um corpo de pedido, como:
   ```json
   {
    "location": "West US",
    "tags": {
      "tagname1": "tagvalue1"
    }
   }
   ```

1. Validar implementação antes de executá-lo ao executar o [validar uma implementação de modelo](/rest/api/resources/deployments/validate) operação. Ao testar a implementação, fornece os parâmetros exatamente como faria ao executar a implementação (mostrada na próxima etapa).

1. Crie uma implementação. Forneça o seu ID de subscrição, o nome do grupo de recursos, o nome da implantação e uma ligação ao seu modelo. Para obter informações sobre o ficheiro de modelo, consulte [ficheiro de parâmetros](#parameter-file). Para obter mais informações sobre a API de REST para criar um grupo de recursos, consulte [criar uma implementação de modelo](/rest/api/resources/deployments/createorupdate). Observe que o **modo** está definida como **Incremental**. Para executar uma implantação completa, definida **modo** ao **concluída**. Tenha cuidado ao usar o modo de conclusão, como inadvertidamente pode eliminar recursos que não estão no seu modelo.

   ```HTTP
   PUT https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>/providers/Microsoft.Resources/deployments/<YourDeploymentName>?api-version=2018-05-01
   ```

   Com um corpo de pedido, como:

   ```json
   {
    "properties": {
      "templateLink": {
        "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
        "contentVersion": "1.0.0.0"
      },
      "mode": "Incremental",
      "parametersLink": {
        "uri": "http://mystorageaccount.blob.core.windows.net/templates/parameters.json",
        "contentVersion": "1.0.0.0"
      }
    }
   }
   ```

    Se quiser iniciar conteúdo de resposta, o conteúdo do pedido ou ambos, inclua **debugSetting** no pedido.

   ```json
   {
    "properties": {
      "templateLink": {
        "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
        "contentVersion": "1.0.0.0"
      },
      "mode": "Incremental",
      "parametersLink": {
        "uri": "http://mystorageaccount.blob.core.windows.net/templates/parameters.json",
        "contentVersion": "1.0.0.0"
      },
      "debugSetting": {
        "detailLevel": "requestContent, responseContent"
      }
    }
   }
   ```

    Pode configurar a sua conta de armazenamento para utilizar um token de assinatura (SAS) de acesso partilhado. Para obter mais informações, consulte [delegar acesso com uma assinatura de acesso partilhado](https://docs.microsoft.com/rest/api/storageservices/delegating-access-with-a-shared-access-signature).

1. Em vez de uma ligação para ficheiros de modelo e parâmetros, pode incluí-los no corpo do pedido.

   ```json
   {
      "properties": {
      "mode": "Incremental",
      "template": {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
          "storageAccountType": {
            "type": "string",
            "defaultValue": "Standard_LRS",
            "allowedValues": [
              "Standard_LRS",
              "Standard_GRS",
              "Standard_ZRS",
              "Premium_LRS"
            ],
            "metadata": {
              "description": "Storage Account type"
            }
          },
          "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]",
            "metadata": {
              "description": "Location for all resources."
            }
          }
        },
        "variables": {
          "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'standardsa')]"
        },
        "resources": [
          {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[variables('storageAccountName')]",
            "apiVersion": "2018-02-01",
            "location": "[parameters('location')]",
            "sku": {
              "name": "[parameters('storageAccountType')]"
            },
            "kind": "StorageV2",
            "properties": {}
          }
        ],
        "outputs": {
          "storageAccountName": {
            "type": "string",
            "value": "[variables('storageAccountName')]"
          }
        }
      },
      "parameters": {
        "location": {
          "value": "eastus2"
        }
      }
    }
   }
   ```

5. Obtenha o estado da implementação do modelo. Para obter mais informações, consulte [obter informações sobre uma implementação de modelo](/rest/api/resources/deployments/get).

   ```HTTP
   GET https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>/providers/Microsoft.Resources/deployments/<YourDeploymentName>?api-version=2018-05-01
   ```

## <a name="redeploy-when-deployment-fails"></a>Implementar novamente quando ocorre uma falha de implementação

Quando uma implementação falhar, pode implementar automaticamente novamente uma implementação anterior, com êxito do seu histórico de implementação. Para especificar a nova implementação, utilize o `onErrorDeployment` propriedade no corpo do pedido.

Para utilizar esta opção, as suas implementações tem de ter nomes exclusivos para que eles podem ser identificados na história. Se não tem nomes exclusivos, a falha na implementação atual poderá substituir a implementação bem-sucedida anterior na história. Só pode utilizar esta opção com implementações de nível de raiz. Implementações a partir de um modelo aninhado não estão disponíveis para reimplantação.

Para Reimplementar a última implementação efetuada com êxito se falhar a implementação atual, utilize:

```json
{
  "properties": {
    "templateLink": {
      "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
      "contentVersion": "1.0.0.0"
    },
    "mode": "Incremental",
    "parametersLink": {
      "uri": "http://mystorageaccount.blob.core.windows.net/templates/parameters.json",
      "contentVersion": "1.0.0.0"
    },
    "onErrorDeployment": {
      "type": "LastSuccessful",
    }
  }
}
```

Para Reimplementar uma implementação específica, se falhar a implementação atual, utilize:

```json
{
  "properties": {
    "templateLink": {
      "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
      "contentVersion": "1.0.0.0"
    },
    "mode": "Incremental",
    "parametersLink": {
      "uri": "http://mystorageaccount.blob.core.windows.net/templates/parameters.json",
      "contentVersion": "1.0.0.0"
    },
    "onErrorDeployment": {
      "type": "SpecificDeployment",
      "deploymentName": "<deploymentname>"
    }
  }
}
```

A implementação especificada tem de ter foi concluída com êxito.

## <a name="parameter-file"></a>Ficheiro de parâmetros

Se utilizar um ficheiro de parâmetros para transmitir valores de parâmetro durante a implementação, terá de criar um ficheiro JSON com um formato semelhante ao seguinte exemplo:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "webSiteName": {
            "value": "ExampleSite"
        },
        "webSiteHostingPlanName": {
            "value": "DefaultPlan"
        },
        "webSiteLocation": {
            "value": "West US"
        },
        "adminPassword": {
            "reference": {
               "keyVault": {
                  "id": "/subscriptions/{guid}/resourceGroups/{group-name}/providers/Microsoft.KeyVault/vaults/{vault-name}"
               }, 
               "secretName": "sqlAdminPassword" 
            }   
        }
   }
}
```

O tamanho do ficheiro de parâmetro não pode ser mais do que 64 KB.

Se precisar de fornecer um valor sensível para um parâmetro (por exemplo, uma palavra-passe), adicione esse valor para um cofre de chaves. Obter o Cofre de chaves durante a implementação, conforme mostrado no exemplo anterior. Para obter mais informações, consulte [transmitir valores seguros durante a implementação](resource-manager-keyvault-parameter.md). 

## <a name="next-steps"></a>Passos Seguintes
* Para especificar como lidar com recursos de que existem no grupo de recursos, mas não estão definidos no modelo, consulte [modos de implementação Azure Resource Manager](deployment-modes.md).
* Para saber mais sobre como lidar com operações assíncronas de REST, veja [monitorizar operações assíncronas de Azure](resource-manager-async-operations.md).
* Para obter um exemplo de implementação de recursos por meio da biblioteca de cliente .NET, consulte [implementar recursos com bibliotecas .NET e um modelo](../virtual-machines/windows/csharp-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Para definir parâmetros no modelo, veja [criação de modelos](resource-group-authoring-templates.md#parameters).
* Para obter documentação de orientação sobre como as empresas podem utilizar o Resource Manager para gerir subscrições de forma eficaz, consulte [Azure enterprise scaffold - prescriptive subscription governance (Andaime empresarial do Azure - governação de subscrições prescritivas)](/azure/architecture/cloud-adoption-guide/subscription-governance).

