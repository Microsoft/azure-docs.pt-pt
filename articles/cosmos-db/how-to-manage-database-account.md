---
title: Saiba como gerir contas de base de dados no Azure Cosmos DB
description: Saiba como gerir contas de base de dados no Azure Cosmos DB
author: christopheranderson
ms.service: cosmos-db
ms.topic: sample
ms.date: 10/17/2018
ms.author: chrande
ms.openlocfilehash: 6efa0bab6327022bfe4a1f6d94a6a135cd1f91f3
ms.sourcegitcommit: 04716e13cc2ab69da57d61819da6cd5508f8c422
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/02/2019
ms.locfileid: "58849063"
---
# <a name="manage-an-azure-cosmos-account"></a>Gerir uma conta do Cosmos do Azure

Este artigo descreve como gerir a sua conta do Azure Cosmos DB. Irá aprender a configurar a multi-homing, adicionar ou remover uma região, configurar várias regiões de escrita e configurar prioridades de ativação pós-falha. 

## <a name="create-a-database-account"></a>Criar uma conta de base de dados

### <a id="create-database-account-via-portal"></a>Portal do Azure

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

### <a id="create-database-account-via-cli"></a>CLI do Azure

```bash
# Create an account
az cosmosdb create --name <Azure Cosmos account name> --resource-group <Resource Group Name>
```

## <a name="configure-clients-for-multi-homing"></a>Configurar clientes para multi-homing

### <a id="configure-clients-multi-homing-dotnet"></a>.NET SDK v2

```csharp
ConnectionPolicy policy = new ConnectionPolicy
    {
        ConnectionMode = ConnectionMode.Direct,
        ConnectionProtocol = Protocol.Tcp,
        UseMultipleWriteLocations = true
    };
policy.SetCurrentLocation("West US 2");

// Pass the connection policy with the preferred locations on it to the client.
DocumentClient client = new DocumentClient(new Uri(this.accountEndpoint), this.accountKey, policy);
```

### <a id="configure-clients-multi-homing-dotnet-v3"></a>SDK de .NET v3 (pré-visualização)

```csharp
CosmosConfiguration config = new CosmosConfiguration("endpoint", "key");
config.UseCurrentRegion("West US");
CosmosClient client = new CosmosClient(config);
```

### <a id="configure-clients-multi-homing-java-async"></a>SDK do Java Async

```java
ConnectionPolicy policy = new ConnectionPolicy();
policy.setUsingMultipleWriteLocations(true);
policy.setPreferredLocations(Collections.singletonList(region));

AsyncDocumentClient client =
    new AsyncDocumentClient.Builder()
        .withMasterKeyOrResourceToken(this.accountKey)
        .withServiceEndpoint(this.accountEndpoint)
        .withConsistencyLevel(ConsistencyLevel.Eventual)
        .withConnectionPolicy(policy).build();
```

### <a id="configure-clients-multi-homing-javascript"></a>SDK do Node.js/JavaScript/TypeScript

```javascript
const connectionPolicy: ConnectionPolicy = new ConnectionPolicy();
connectionPolicy.UseMultipleWriteLocations = true;
connectionPolicy.PreferredLocations = [region];

const client = new CosmosClient({
  endpoint: config.endpoint,
  auth: { masterKey: config.key },
  connectionPolicy,
  consistencyLevel: ConsistencyLevel.Eventual
});
```

### <a id="configure-clients-multi-homing-python"></a>SDK do Python

```python
connection_policy = documents.ConnectionPolicy()
connection_policy.UseMultipleWriteLocations = True
connection_policy.PreferredLocations = [region]

client = cosmos_client.CosmosClient(self.account_endpoint, {'masterKey': self.account_key}, connection_policy, documents.ConsistencyLevel.Session)
```

## <a name="addremove-regions-from-your-database-account"></a>Adicionar/remover regiões da conta de base de dados

### <a id="add-remove-regions-via-portal"></a>Portal do Azure

1. Aceda à sua conta do Azure Cosmos DB e abra o **replicar dados globalmente** menu.

2. Para adicionar regiões, selecione os Hexágonos no mapa com o **+** etiqueta que correspondem à sua região pretendida. Para adicionar uma região, selecione o **+ adicionar região** opção e selecione uma região no menu pendente.

3. Para remover regiões, limpe um ou mais regiões do mapa de selecionando os Hexágonos azuis com marcas de seleção. Ou selecione "lixeira" (🗑) ícone ao lado de região no lado direito.

4. Para guardar as alterações, selecione **OK**.

   ![Adicionar ou remover o menu de regiões](./media/how-to-manage-database-account/add-region.png)

No modo de escrita única região, é possível remover a região de escrita. Deve efetuar a ativação pós-falha para uma região diferente antes de poder eliminar essa região de escrita atual.

Em várias regiões crie modo, pode adicionar ou remover qualquer região, se tiver pelo menos uma região.

### <a id="add-remove-regions-via-cli"></a>CLI do Azure

```bash
# Given an account created with 1 region like so
az cosmosdb create --name <Azure Cosmos account name> --resource-group <Resource Group name> --locations eastus=0

# Add a new region by adding another region to the list
az cosmosdb update --name <Azure Cosmos account name> --resource-group <Resource Group name> --locations eastus=0 westus=1

# Remove a region by removing a region from the list
az cosmosdb update --name <Azure Cosmos account name> --resource-group <Resource Group name> --locations westus=0
```

## <a name="configure-multiple-write-regions"></a>Configurar várias regiões de escrita

### <a id="configure-multiple-write-regions-portal"></a>Portal do Azure

Quando criar uma conta de base de dados, certifique-se de que a definição **Escritas de Várias Regiões** está ativada.

![Captura de criação ecrã do Azure Cosmos conta](./media/how-to-manage-database-account/account-create.png)

### <a id="configure-multiple-write-regions-cli"></a>CLI do Azure

```bash
az cosmosdb create --name <Azure Cosmos account name> --resource-group <Resource Group name> --enable-multiple-write-locations true
```

### <a id="configure-multiple-write-regions-arm"></a>Modelo do Resource Manager

O seguinte código JSON é um exemplo de um modelo Azure Resource Manager. Pode usá-lo para implementar uma conta do Azure Cosmos DB com uma política de consistência de estagnação limitada. O intervalo de envelhecimento máximo estiver definido como 5 segundos. O número máximo de pedidos obsoletos, que é pela tolerar é o valor 100. Para saber mais sobre a sintaxe e o formato de modelo do Resource Manager, veja [Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md).

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "name": {
            "type": "String"
        },
        "location": {
            "type": "String"
        },
        "locationName": {
            "type": "String"
        },
        "defaultExperience": {
            "type": "String"
        }
    },
    "resources": [
        {
            "type": "Microsoft.DocumentDb/databaseAccounts",
            "kind": "GlobalDocumentDB",
            "name": "[parameters('name')]",
            "apiVersion": "2015-04-08",
            "location": "[parameters('location')]",
            "tags": {
                "defaultExperience": "[parameters('defaultExperience')]"
            },
            "properties": {
                "databaseAccountOfferType": "Standard",
                "consistencyPolicy": {
                    "defaultConsistencyLevel": "BoundedStaleness",
                    "maxIntervalInSeconds": 5,
                    "maxStalenessPrefix": 100
                },
                "locations": [
                    {
                        "id": "[concat(parameters('name'), '-', parameters('location'))]",
                        "failoverPriority": 0,
                        "locationName": "[parameters('locationName')]"
                    }
                ],
                "isVirtualNetworkFilterEnabled": false,
                "enableMultipleWriteLocations": true,
                "virtualNetworkRules": [],
                "dependsOn": []
            }
        }
    ]
}
```


## <a id="manual-failover"></a>Ativar a ativação pós-falha manual para a sua conta do Azure Cosmos DB

### <a id="enable-manual-failover-via-portal"></a>Portal do Azure

1. Aceda à sua conta do Azure Cosmos DB e abra o **replicar dados globalmente** menu.

2. Na parte superior do menu, selecione **ativação pós-falha do Manual**.

   ![Menu Replicar dados globalmente](./media/how-to-manage-database-account/replicate-data-globally.png)

3. Sobre o **ativação pós-falha do Manual** menu, selecione a sua nova região de escrita. Selecione a caixa de verificação para indicar que compreende que esta opção altera a sua região de escrita.

4. Para acionar a ativação pós-falha, selecione **OK**.

   ![Menu do portal de ativação pós-falha manual](./media/how-to-manage-database-account/manual-failover.png)

### <a id="enable-manual-failover-via-cli"></a>CLI do Azure

```bash
# Given your account currently has regions with priority like so: eastus=0 westus=1
# Change the priority order to trigger a failover of the write region
az cosmosdb update --name <Azure Cosmos account name> --resource-group <Resource Group name> --locations westus=0 eastus=1
```

## <a id="automatic-failover"></a>Ativar a ativação pós-falha automática para a sua conta do Azure Cosmos DB

### <a id="enable-automatic-failover-via-portal"></a>Portal do Azure

1. A partir da sua conta do Azure Cosmos DB, abra a **replicar dados globalmente** painel. 

2. Na parte superior do painel, selecione **a ativação pós-falha automática**.

   ![Menu Replicar dados globalmente](./media/how-to-manage-database-account/replicate-data-globally.png)

3. Na **a ativação pós-falha automática** painel, certifique-se de que **ativar a ativação pós-falha automática** está definida como **ON**. 

4. Selecione **Guardar**.

   ![Menu do portal de ativação pós-falha automática](./media/how-to-manage-database-account/automatic-failover.png)

Também pode definir suas prioridades de ativação pós-falha neste menu.

### <a id="enable-automatic-failover-via-cli"></a>CLI do Azure

```bash
# Enable automatic failover on account creation
az cosmosdb create --name <Azure Cosmos account name> --resource-group <Resource Group name> --enable-automatic-failover true

# Enable automatic failover on an existing account
az cosmosdb update --name <Azure Cosmos account name> --resource-group <Resource Group name> --enable-automatic-failover true

# Disable automatic failover on an existing account
az cosmosdb update --name <Azure Cosmos account name> --resource-group <Resource Group name> --enable-automatic-failover false
```

## <a name="set-failover-priorities-for-your-azure-cosmos-db-account"></a>Definir prioridades de ativação pós-falha para a sua conta do Azure Cosmos DB

### <a id="set-failover-priorities-via-portal"></a>Portal do Azure

1. A partir da sua conta do Azure Cosmos DB, abra a **replicar dados globalmente** painel. 

2. Na parte superior do painel, selecione **a ativação pós-falha automática**.

   ![Menu Replicar dados globalmente](./media/how-to-manage-database-account/replicate-data-globally.png)

3. Na **a ativação pós-falha automática** painel, certifique-se de que **ativar a ativação pós-falha automática** está definida como **ON**. 

4. Para modificar a prioridade de ativação pós-falha, arraste as regiões de leitura por meio de três pontos no lado esquerdo da linha que aparecem quando paira o rato sobre os mesmos. 

5. Selecione **Guardar**.

   ![Menu do portal de ativação pós-falha automática](./media/how-to-manage-database-account/automatic-failover.png)

Não é possível modificar a região de escrita neste menu. Para alterar a região de escrita manualmente, tem de fazer uma ativação pós-falha manual.

### <a id="set-failover-priorities-via-cli"></a>CLI do Azure

```bash
# Assume region order is initially eastus=0 westus=1 automatic failover on account creation
az cosmosdb failover-priority-change --name <Azure Cosmos account name> --resource-group <Resource Group name> --failover-policies westus=0 eastus=1
```

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre como gerir os conflitos de dados e níveis de consistência no Azure Cosmos DB. Consulte os seguintes artigos:

* [Gerir a consistência](how-to-manage-consistency.md)
* [Gerir conflitos entre regiões](how-to-manage-conflicts.md)

