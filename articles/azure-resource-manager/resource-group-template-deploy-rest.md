---
title: Implementar recursos com a REST API e o modelo | Documentos da Microsoft
description: Utilize o Azure Resource Manager e API de REST do Resource Manager para implementar recursos no Azure. Os recursos são definidos num modelo do Resource Manager.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: tomfitz
ms.openlocfilehash: 42f6ce96cf339e90ed0a0dcdbdb3f1b6924430e9
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67206404"
---
# <a name="deploy-resources-with-resource-manager-templates-and-resource-manager-rest-api"></a>Implementar recursos com modelos do Resource Manager e API REST do Resource Manager

Este artigo explica como utilizar a API de REST do Resource Manager com modelos do Resource Manager para implementar os seus recursos no Azure.  

Pode optar por incluir o modelo no corpo do pedido ou ligação para um ficheiro. Quando utilizar um ficheiro, pode ser um ficheiro local ou um arquivo externo que está disponível através de um URI. Quando o modelo estiver numa conta de armazenamento, pode restringir o acesso ao modelo e fornecer um token de assinatura (SAS) de acesso partilhado durante a implementação.

## <a name="deployment-scope"></a>Escopo da implantação

Pode direcionar a sua implementação para um grupo de gestão, uma subscrição do Azure ou um grupo de recursos. Na maioria dos casos, irá direcionar implementações para um grupo de recursos. Utilize implementações de subscrição ou grupo de gestão para aplicar políticas e as atribuições de funções em âmbito especificado. Também utilizar implementações de subscrição para criar um grupo de recursos e implementar recursos no mesmo. Dependendo do escopo da implantação, é usar comandos diferentes.

Para implementar um **grupo de recursos**, utilize [criar implementações -](/rest/api/resources/deployments/createorupdate). O pedido é enviado para:

```HTTP
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.Resources/deployments/{deploymentName}?api-version=2019-05-01
```

Para implementar um **subscrição**, utilize [implementações - criar no âmbito da subscrição](/rest/api/resources/deployments/createorupdateatsubscriptionscope). O pedido é enviado para:

```HTTP
PUT https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Resources/deployments/{deploymentName}?api-version=2019-05-01
```

Para implementar um **grupo de gestão**, utilize [implementações - criar no âmbito do grupo de gestão](/rest/api/resources/deployments/createorupdateatmanagementgroupscope). O pedido é enviado para:

```HTTP
PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{groupId}/providers/Microsoft.Resources/deployments/{deploymentName}?api-version=2019-05-01
```

Os exemplos neste artigo utilizam implementações do grupo de recursos. Para obter mais informações sobre implementações de subscrição, veja [criar grupos de recursos e recursos ao nível da subscrição](deploy-to-subscription.md).

## <a name="deploy-with-the-rest-api"></a>Implementar com a API REST

1. Definir [parâmetros comuns e os cabeçalhos](/rest/api/azure/), incluindo os tokens de autenticação.

1. Se não tiver um grupo de recursos existente, crie um grupo de recursos. Forneça o ID da subscrição, o nome do novo grupo de recursos e localização que precisa para a sua solução. Para obter mais informações, consulte [criar um grupo de recursos](/rest/api/resources/resourcegroups/createorupdate).

   ```HTTP
   PUT https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>?api-version=2019-05-01
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

1. Para implementar um modelo, forneça o ID da subscrição, o nome do grupo de recursos, o nome da implementação no URI do pedido. 

   ```HTTP
   PUT https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>/providers/Microsoft.Resources/deployments/<YourDeploymentName>?api-version=2019-05-01
   ```

   No corpo do pedido, forneça uma ligação para o ficheiro de modelo e o parâmetro. Observe que o **modo** está definida como **Incremental**. Para executar uma implantação completa, definida **modo** ao **concluída**. Tenha cuidado ao usar o modo de conclusão, como inadvertidamente pode eliminar recursos que não estão no seu modelo.

   ```json
   {
    "properties": {
      "templateLink": {
        "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
        "contentVersion": "1.0.0.0"
      },
      "parametersLink": {
        "uri": "http://mystorageaccount.blob.core.windows.net/templates/parameters.json",
        "contentVersion": "1.0.0.0"
      },
      "mode": "Incremental"
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
      "parametersLink": {
        "uri": "http://mystorageaccount.blob.core.windows.net/templates/parameters.json",
        "contentVersion": "1.0.0.0"
      },
      "mode": "Incremental",
      "debugSetting": {
        "detailLevel": "requestContent, responseContent"
      }
    }
   }
   ```

    Pode configurar a sua conta de armazenamento para utilizar um token de assinatura (SAS) de acesso partilhado. Para obter mais informações, consulte [delegar acesso com uma assinatura de acesso partilhado](https://docs.microsoft.com/rest/api/storageservices/delegating-access-with-a-shared-access-signature).

1. Em vez de uma ligação para ficheiros de modelo e parâmetros, pode incluí-los no corpo do pedido. O exemplo seguinte mostra o corpo do pedido com o modelo e parâmetros inline:

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

1. Para obter o estado da implementação do modelo, utilize [implementações - obter](/rest/api/resources/deployments/get).

   ```HTTP
   GET https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>/providers/Microsoft.Resources/deployments/<YourDeploymentName>?api-version=2018-05-01
   ```

## <a name="redeploy-when-deployment-fails"></a>Implementar novamente quando ocorre uma falha de implementação

Esta funcionalidade também é conhecido como *reversão com o erro*. Quando uma implementação falhar, pode implementar automaticamente novamente uma implementação anterior, com êxito do seu histórico de implementação. Para especificar a nova implementação, utilize o `onErrorDeployment` propriedade no corpo do pedido. Essa funcionalidade é útil se tem um bom estado conhecido para a sua implementação de infraestrutura e pretenda reverter para este estado. Há uma série de limitações e restrições:

- A reimplementação é executada exatamente como foi anteriormente executada com os mesmos parâmetros. Não é possível alterar os parâmetros.
- A implementação anterior for executada utilizando o [modo de conclusão](./deployment-modes.md#complete-mode). São eliminados todos os recursos não incluídos na implementação anterior e quaisquer configurações de recursos são definidas para o seu estado anterior. Certifique-se de que compreende totalmente o [modos de implementação](./deployment-modes.md).
- A reimplementação afeta apenas os recursos, quaisquer alterações de dados não são afetadas.
- Esta funcionalidade só é suportada em implementações de grupo de recursos, não implementações ao nível de subscrição. Para obter mais informações sobre a implementação de nível de subscrição, veja [criar grupos de recursos e recursos ao nível da subscrição](./deploy-to-subscription.md).

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

- Para especificar como lidar com recursos de que existem no grupo de recursos, mas não estão definidos no modelo, consulte [modos de implementação Azure Resource Manager](deployment-modes.md).
- Para saber mais sobre como lidar com operações assíncronas de REST, veja [monitorizar operações assíncronas de Azure](resource-manager-async-operations.md).
- Para saber mais sobre modelos, veja [compreender a estrutura e a sintaxe de modelos Azure Resource Manager](resource-group-authoring-templates.md).

