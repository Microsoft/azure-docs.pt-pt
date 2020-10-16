---
title: Evite que os recursos DB da Azure Cosmos sejam apagados ou alterados
description: Utilize fechaduras de recursos Azure para evitar que os recursos DB da Azure Cosmos sejam eliminados ou alterados.
author: markjbrown
ms.service: cosmos-db
ms.topic: how-to
ms.date: 10/06/2020
ms.author: mjbrown
ms.openlocfilehash: b23fb22a99705e1bf37de7a7982513ff692d8f6a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91771054"
---
# <a name="prevent-azure-cosmos-db-resources-from-being-deleted-or-changed"></a>Evite que os recursos DB da Azure Cosmos sejam apagados ou alterados

Como administrador, poderá ter de bloquear uma conta, base de dados ou contentor da Azure Cosmos para evitar que outros utilizadores da sua organização apaguem ou modifiquem acidentalmente recursos críticos. Pode definir o nível do bloqueio para CanNotDelete ou ReadOnly.

- **CanNotDelete** significa que os utilizadores autorizados ainda podem ler e modificar um recurso, mas não podem eliminar o recurso.
- **ReadOnly** significa que os utilizadores autorizados podem ler um recurso, mas não podem eliminar ou atualizar o recurso. A aplicação deste bloqueio é semelhante a restringir todos os utilizadores autorizados às permissões concedidas pela função Reader.

## <a name="how-locks-are-applied"></a>Como os bloqueios são aplicados

Quando se aplica uma fechadura no âmbito dos pais, todos os recursos dentro desse âmbito herdam a mesma fechadura. Até os recursos que adicionas mais tarde herda a fechadura do progenitor. O bloqueio mais restritivo na herança tem precedência.

Ao contrário do controlo de acesso baseado em funções, pode utilizar a gestão de bloqueios para aplicar uma restrição a todos os utilizadores e a todas as funções. Para saber sobre o RBAC para Azure Cosmos DB ver, [controlo de acesso baseado em papéis em Azure Cosmos DB](role-based-access-control.md).

Os bloqueios do Resource Manager aplicam-se apenas a operações que ocorrem no painel de gestão, o que consiste em operações enviadas para https://management.azure.com. Os bloqueios não restringem a forma como os recursos desempenham as suas próprias funções. As alterações dos recursos são restritas, mas as operações dos recursos não o são. Por exemplo, um bloqueio ReadOnly num recipiente Azure Cosmos impede-o de eliminar ou modificar o recipiente. Não o impede de criar, atualizar ou eliminar dados no recipiente. As transações de dados são permitidas porque essas operações não são enviadas para https://management.azure.com.

## <a name="manage-locks"></a>Gerir fechaduras

> [!WARNING]
> Os bloqueios de recursos não funcionam para alterações feitas pelos utilizadores que acedem ao Azure Cosmos DB usando chaves de conta, a menos que a conta Azure Cosmos seja bloqueada pela primeira vez, permitindo a propriedade desativadaTaWriteAccess. Deve ter cuidado antes de permitir que esta propriedade garanta que não quebra aplicações existentes que escamem os recursos utilizando quaisquer ferramentas SDK, portal Azure ou 3ª parte que se conectam através de chaves de conta e modificam recursos como alterar o rendimento, atualizar políticas de índice, etc. Para saber mais e passar por uma lista de verificação para garantir que as suas aplicações continuam a funcionar ver, [Prevenindo alterações dos SDKs Azure Cosmos DB](role-based-access-control.md#prevent-sdk-changes)

### <a name="powershell"></a>PowerShell

```powershell
$resourceGroupName = "myResourceGroup"
$accountName = "my-cosmos-account"
$lockName = "$accountName-Lock"

# First, update the account to prevent changes by anything that connects via account keys
Update-AzCosmosDBAccount -ResourceGroupName $resourceGroupName -Name $accountName -DisableKeyBasedMetadataWriteAccess true

# Create a Delete Lock on an Azure Cosmos account resource and all child resources
New-AzResourceLock `
    -ApiVersion "2020-04-01" `
    -ResourceType "Microsoft.DocumentDB/databaseAccounts" `
    -ResourceGroupName $resourceGroupName `
    -ResourceName $accountName `
    -LockName $lockName `
    -LockLevel "CanNotDelete" # CanNotDelete or ReadOnly
```

### <a name="azure-cli"></a>CLI do Azure

```bash
resourceGroupName='myResourceGroup'
accountName='my-cosmos-account'
$lockName="$accountName-Lock"

# First, update the account to prevent changes by anything that connects via account keys
az cosmosdb update  --name $accountName --resource-group $resourceGroupName  --disable-key-based-metadata-write-access true

# Create a Delete Lock on an Azure Cosmos account resource
az lock create --name $lockName \
    --resource-group $resourceGroupName \
    --resource-type Microsoft.DocumentDB/databaseAccount \
    --lock-type 'CanNotDelete' # CanNotDelete or ReadOnly \
    --resource $accountName
```

### <a name="template"></a>Modelo

Ao aplicar uma fechadura num recurso DB da Azure Cosmos, utilize os seguintes formatos:

- nome - `{resourceName}/Microsoft.Authorization/{lockName}`
- tipo - `{resourceProviderNamespace}/{resourceType}/providers/locks`

> [!IMPORTANT]
> Ao modificar uma conta Azure Cosmos existente, certifique-se de incluir as outras propriedades da sua conta e recursos infantis ao redparear com esta propriedade. Não implemente este modelo como está ou irá redefinir todas as propriedades da sua conta.

```json
"resources": [
    {
        "type": "Microsoft.DocumentDB/databaseAccounts",
        "name": "[variables('accountName')]",
        "apiVersion": "2020-04-01",
        "kind": "GlobalDocumentDB",
        "location": "[parameters('location')]",
        "properties": {
            "consistencyPolicy": "[variables('consistencyPolicy')[parameters('defaultConsistencyLevel')]]",
            "locations": "[variables('locations')]",
            "databaseAccountOfferType": "Standard",
            "enableAutomaticFailover": "[parameters('automaticFailover')]",
            "disableKeyBasedMetadataWriteAccess": true
        }
    },
    {
        "type": "Microsoft.DocumentDB/databaseAccounts/providers/locks",
        "apiVersion": "2020-04-01",
        "name": "[concat(variables('accountName'), '/Microsoft.Authorization/siteLock')]",
        "dependsOn": [
        "[resourceId('Microsoft.DocumentDB/databaseAccounts', variables('accountName'))]"
        ],
        "properties": {
        "level": "CanNotDelete",
        "notes": "Cosmos account should not be deleted."
        }
    }
]
```

## <a name="next-steps"></a>Passos seguintes

- [Visão geral das fechaduras do gestor de recursos Azure](../azure-resource-manager/management/lock-resources.md)
