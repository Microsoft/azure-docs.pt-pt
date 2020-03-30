---
title: Configure as chaves geridas pelo cliente no Azure Data Explorer utilizando o modelo do Gestor de Recursos Azure
description: Este artigo descreve como configurar a encriptação de chaves geridas pelo cliente nos seus dados no Azure Data Explorer utilizando o modelo do Gestor de Recursos Do Azure.
author: saguiitay
ms.author: itsagui
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/06/2020
ms.openlocfilehash: 454a80089b5f74d4a70015ffcd03d0212e8c08a6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80297923"
---
# <a name="configure-customer-managed-keys-using-the-azure-resource-manager-template"></a>Configure as chaves geridas pelo cliente utilizando o modelo do Gestor de Recursos Azure

> [!div class="op_single_selector"]
> * [Portal](customer-managed-keys-portal.md)
> * [C #](customer-managed-keys-csharp.md)
> * [Modelo Azure Resource Manager](customer-managed-keys-resource-manager.md)

[!INCLUDE [data-explorer-configure-customer-managed-keys](../../includes/data-explorer-configure-customer-managed-keys.md)]

[!INCLUDE [data-explorer-configure-customer-managed-keys part 2](../../includes/data-explorer-configure-customer-managed-keys-b.md)]

## <a name="configure-encryption-with-customer-managed-keys"></a>Configure encriptação com chaves geridas pelo cliente

Nesta secção, configura chaves geridas pelo cliente utilizando modelos do Gestor de Recursos Azure. Por padrão, a encriptação do Azure Data Explorer utiliza chaves geridas pela Microsoft. Neste passo, configure o seu cluster Azure Data Explorer para utilizar as chaves geridas pelo cliente e especificar a chave para se associar ao cluster.

Pode implementar o modelo do Gestor de Recursos Azure utilizando o portal Azure ou utilizando o PowerShell.

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

## <a name="update-the-key-version"></a>Atualizar a versão chave

Quando criar uma nova versão de uma chave, terá de atualizar o cluster para utilizar a nova versão. Primeiro, `Get-AzKeyVaultKey` ligue para obter a versão mais recente da chave. Em seguida, atualize as propriedades chave do cofre do cluster para usar a nova versão da chave, como mostrado na [encriptação Configure com chaves geridas pelo cliente](#configure-encryption-with-customer-managed-keys).

## <a name="next-steps"></a>Passos seguintes

* [Clusters Secure Azure Data Explorer em Azure](security.md)
* [Configure identidades geridas para o seu cluster Azure Data Explorer](managed-identities.md)
* [Proteja o seu cluster no Azure Data Explorer - portal Azure,](manage-cluster-security.md) permitindo a encriptação em repouso.
* [Configure as chaves geridas pelo cliente utilizando C #](customer-managed-keys-csharp.md)

