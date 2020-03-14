---
title: Implementar recursos com API rest e modelo
description: Utilize o Gestor de Recursos Azure e o Gestor de Recursos REST API para mobilizar recursos para o Azure. Os recursos são definidos num modelo do Resource Manager.
ms.topic: conceptual
ms.date: 06/04/2019
ms.openlocfilehash: fc386f51073c256fd083a04bbed39316784827b1
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79273816"
---
# <a name="deploy-resources-with-resource-manager-templates-and-resource-manager-rest-api"></a>Implementar recursos com modelos do Resource Manager e API REST do Resource Manager

Este artigo explica como usar a API REST API do Gestor de Recursos com modelos de Gestor de Recursos para implantar os seus recursos para o Azure.

Pode incluir o seu modelo no corpo de pedido ou ligar-se a um ficheiro. Ao utilizar um ficheiro, pode ser um ficheiro local ou um ficheiro externo que está disponível através de um URI. Quando o seu modelo está numa conta de armazenamento, pode restringir o acesso ao modelo e fornecer um símbolo de assinatura de acesso partilhado (SAS) durante a implementação.

## <a name="deployment-scope"></a>Âmbito de implantação

Pode direcionar a sua implementação para um grupo de gestão, uma subscrição Azure ou um grupo de recursos. Na maioria dos casos, você vai direcionar as implementações para um grupo de recursos. Utilize implementações de grupos de gestão ou subscrições para aplicar políticas e atribuições de funções em todo o âmbito especificado. Também utiliza implementações de subscrição para criar um grupo de recursos e implementar recursos para o mesmo. Dependendo do alcance da implantação, utiliza-se comandos diferentes.

Para implantar num grupo de **recursos,** utilize [implantações - Criar](/rest/api/resources/deployments/createorupdate). O pedido é enviado para:

```HTTP
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.Resources/deployments/{deploymentName}?api-version=2019-05-01
```

Para implementar para uma **subscrição**, use [Implementações - Criar no Âmbito](/rest/api/resources/deployments/createorupdateatsubscriptionscope)de Subscrição . O pedido é enviado para:

```HTTP
PUT https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Resources/deployments/{deploymentName}?api-version=2019-05-01
```

Para obter mais informações sobre implementações de nível de subscrição, consulte [Criar grupos de recursos e recursos ao nível da subscrição](deploy-to-subscription.md).

Para implantar num grupo de **gestão,** use [Implantações - Criar no Âmbito do Grupo de Gestão](/rest/api/resources/deployments/createorupdateatmanagementgroupscope). O pedido é enviado para:

```HTTP
PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{groupId}/providers/Microsoft.Resources/deployments/{deploymentName}?api-version=2019-05-01
```

Para obter mais informações sobre as implementações a nível do grupo de gestão, consulte Criar recursos ao nível do grupo de [gestão.](deploy-to-management-group.md)

Os exemplos deste artigo utilizam as implantações de grupos de recursos.

## <a name="deploy-with-the-rest-api"></a>Implementar com a API REST

1. Defina [parâmetros e cabeçalhos comuns,](/rest/api/azure/)incluindo fichas de autenticação.

1. Se não tiver um grupo de recursos existente, crie um grupo de recursos. Forneça o seu ID de subscrição, o nome do novo grupo de recursos e a localização que necessita para a sua solução. Para mais informações, consulte [Criar um grupo de recursos](/rest/api/resources/resourcegroups/createorupdate).

   ```HTTP
   PUT https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>?api-version=2019-05-01
   ```

   Com um corpo de pedido como:

   ```json
   {
    "location": "West US",
    "tags": {
      "tagname1": "tagvalue1"
    }
   }
   ```

1. Valide a sua implantação antes de executá-la executando a operação de implementação do [modelo.](/rest/api/resources/deployments/validate) Ao testar a implementação, forneça parâmetros exatamente como faria ao executar a implementação (mostrada no passo seguinte).

1. Para implementar um modelo, forneça o seu ID de subscrição, o nome do grupo de recursos, o nome da implementação no pedido URI.

   ```HTTP
   PUT https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>/providers/Microsoft.Resources/deployments/<YourDeploymentName>?api-version=2019-05-01
   ```

   No organismo de pedido, forneça um link para o seu modelo e ficheiro de parâmetro. Para obter mais informações sobre o ficheiro do parâmetro, consulte o [ficheiro de parâmetro sinuoso Do Gestor de Recursos](parameter-files.md).

   Note que o **modo** está definido para **Incremental**. Para executar uma implementação completa, deset o **modo** para **completar**. Tenha cuidado ao utilizar o modo completo, pois pode eliminar inadvertidamente recursos que não estão no seu modelo.

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

    Se pretender registar conteúdo de resposta, solicite conteúdo ou ambos, inclua **depuração** no pedido.

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

    Pode configurar a sua conta de armazenamento para utilizar um símbolo de assinatura de acesso partilhado (SAS). Para mais informações, consulte [Delegar acesso com uma Assinatura de Acesso Partilhado](/rest/api/storageservices/delegating-access-with-a-shared-access-signature).

    Se precisar de fornecer um valor sensível para um parâmetro (como uma palavra-passe), adicione esse valor a um cofre chave. Recupere o cofre da chave durante a implantação, como mostrado no exemplo anterior. Para mais informações, consulte [o Passe valores seguros durante a implementação](key-vault-parameter.md).

1. Em vez de se ligar aos ficheiros para o modelo e parâmetros, pode incluí-los no corpo de pedido. O exemplo seguinte mostra o corpo de pedido com o modelo e o parâmetro inline:

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
            "apiVersion": "2018-02-01",
            "name": "[variables('storageAccountName')]",
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

1. Para obter o estado da implementação do modelo, utilize [implementações - Obter](/rest/api/resources/deployments/get).

   ```HTTP
   GET https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>/providers/Microsoft.Resources/deployments/<YourDeploymentName>?api-version=2018-05-01
   ```

## <a name="next-steps"></a>Passos seguintes

- Para voltar a uma implementação bem sucedida quando tiver um erro, consulte [o Rollback no erro para uma implementação bem sucedida](rollback-on-error.md).
- Para especificar como lidar com os recursos que existem no grupo de recursos mas não estão definidos no modelo, consulte os modos de implementação do Gestor de [Recursos Do Azure](deployment-modes.md).
- Para aprender sobre como lidar com operações de REST assíncronos, consulte as operações de [Track assíncronos Azure](../management/async-operations.md).
- Para saber mais sobre os modelos, consulte [Compreender a estrutura e a sintaxe dos modelos do Gestor de Recursos Azure.](template-syntax.md)

