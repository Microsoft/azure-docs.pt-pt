---
title: Configure identidades geridas com Azure AD para a sua conta DB Azure Cosmos
description: Saiba como configurar identidades geridas com o Azure Ative Directory para a sua conta DB Azure Cosmos
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: how-to
ms.date: 04/02/2021
ms.author: thweiss
ms.openlocfilehash: 30efaed09a400611861bdd3adeae1f650054b405
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/02/2021
ms.locfileid: "106231500"
---
# <a name="configure-managed-identities-with-azure-active-directory-for-your-azure-cosmos-db-account"></a>Configure identidades geridas com Azure Ative Directory para a sua conta DB Azure Cosmos
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Identidades geridas para recursos Azure fornecem aos serviços Azure uma identidade gerida automaticamente no Azure Ative Directory. Este artigo mostra como criar uma identidade gerida para as contas DB da Azure Cosmos.

> [!NOTE]
> Apenas identidades geridas atribuídas pelo sistema são atualmente suportadas pela Azure Cosmos DB.

## <a name="prerequisites"></a>Pré-requisitos

- Se não está familiarizado com identidades geridas para recursos Azure, veja [o que são identidades geridas para os recursos do Azure?](../active-directory/managed-identities-azure-resources/overview.md) Para saber mais sobre tipos de identidade geridos, consulte [tipos de identidade geridos](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types).
- Para configurar identidades geridas, a sua conta precisa de ter a [função de Contribuinte de Conta DocumentDB](../role-based-access-control/built-in-roles.md#documentdb-account-contributor).

## <a name="add-a-system-assigned-identity"></a>Adicionar uma identidade atribuída ao sistema

### <a name="using-an-azure-resource-manager-arm-template"></a>Usando um modelo de Gestor de Recursos Azure (ARM)

> [!IMPORTANT]
> Certifique-se de que utiliza um `apiVersion` de ou superior ao trabalhar com `2021-03-15` identidades geridas.

Para permitir uma identidade atribuída ao sistema numa nova conta DB Azure Cosmos, inclua a seguinte propriedade na definição de recurso:

```json
"identity": {
    "type": "SystemAssigned"
}
```

A `resources` secção do seu modelo ARM deve então parecer o seguinte:

```json
"resources": [
    {
        "type": " Microsoft.DocumentDB/databaseAccounts",
        "identity": {
            "type": "SystemAssigned"
        },
        // ...
    },
    // ...
 ]
```

Depois de a sua conta DB Azure Cosmos ter sido criada ou atualizada, apresentará a seguinte propriedade:

```json
"identity": {
    "type": "SystemAssigned",
    "tenantId": "<azure-ad-tenant-id>",
    "principalId": "<azure-ad-principal-id>"
}
```

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [identidades geridas para recursos Azure](../active-directory/managed-identities-azure-resources/overview.md)
- Saiba mais sobre [chaves geridas pelo cliente no Azure Cosmos DB](how-to-setup-cmk.md)
