---
title: Configurar chaves gerenciadas pelo cliente no Azure Data Explorer usando o modelo de Azure Resource Manager
description: Este artigo descreve como configurar a criptografia de chaves gerenciadas pelo cliente em seus dados no Azure Data Explorer usando o modelo de Azure Resource Manager.
author: saguiitay
ms.author: itsagui
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/06/2020
ms.openlocfilehash: d0f7085342f972f227fc549c423672296697d7de
ms.sourcegitcommit: d9ec6e731e7508d02850c9e05d98d26c4b6f13e6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/20/2020
ms.locfileid: "76281262"
---
# <a name="configure-customer-managed-keys-using-the-azure-resource-manager-template"></a>Configurar chaves gerenciadas pelo cliente usando o modelo de Azure Resource Manager

> [!div class="op_single_selector"]
> * [C#](customer-managed-keys-csharp.md)
> * [Modelo do Azure Resource Manager](customer-managed-keys-resource-manager.md)

[!INCLUDE [data-explorer-configure-customer-managed-keys](../../includes/data-explorer-configure-customer-managed-keys.md)]

## <a name="configure-encryption-with-customer-managed-keys"></a>Configurar a criptografia com chaves gerenciadas pelo cliente

Nesta seção, você configurará chaves gerenciadas pelo cliente usando modelos de Azure Resource Manager. Por padrão, a criptografia de Data Explorer do Azure usa chaves gerenciadas pela Microsoft. Nesta etapa, configure seu cluster de Data Explorer do Azure para usar chaves gerenciadas pelo cliente e especifique a chave a ser associada ao cluster.

Você pode implantar o modelo de Azure Resource Manager usando o portal do Azure ou usando o PowerShell.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "clusterName": {
      "type": "string",
      "defaultValue": "[concat('kusto', uniqueString(resourceGroup().id))]",
      "metadata": {
        "description": "Name of the cluster to create"
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
  "variables": {},
  "resources": [
    {
      "name": "[parameters('clusterName')]",
      "type": "Microsoft.Kusto/clusters",
      "sku": {
        "name": "Standard_D13_v2",
        "tier": "Standard",
        "capacity": 2
      },
      "apiVersion": "2019-09-07",
      "location": "[parameters('location')]",
      "properties": {
        "keyVaultProperties": {
          "keyVaultUri": "<keyVaultUri>",
          "keyName": "<keyName>",
          "keyVersion": "<keyVersion"
        }
      }
    }
  ]
}
```

## <a name="update-the-key-version"></a>Atualizar a versão de chave

Ao criar uma nova versão de uma chave, você precisará atualizar o cluster para usar a nova versão. Primeiro, chame `Get-AzKeyVaultKey` para obter a versão mais recente da chave. Em seguida, atualize as propriedades do cofre de chaves do cluster para usar a nova versão da chave, conforme mostrado em [Configurar criptografia com chaves gerenciadas pelo cliente](#configure-encryption-with-customer-managed-keys).

## <a name="next-steps"></a>Passos seguintes

* [Proteger clusters de Data Explorer do Azure no Azure](security.md)
* [Configurar identidades gerenciadas para o cluster de Data Explorer do Azure](managed-identities.md)
* [Proteja seu cluster no Azure data Explorer-portal do Azure](manage-cluster-security.md) habilitando a criptografia em repouso.
* [Configurar chaves gerenciadas pelo cliente usando oC#](customer-managed-keys-csharp.md)

