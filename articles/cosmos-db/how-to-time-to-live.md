---
title: Saiba como configurar e gerenciar a vida útil no Azure Cosmos DB
description: Saiba como configurar e gerenciar a vida útil no Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/17/2019
ms.author: mjbrown
ms.openlocfilehash: ddda7b96147892efb38cb0405120db3613e98cf8
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/18/2019
ms.locfileid: "71104861"
---
# <a name="configure-time-to-live-in-azure-cosmos-db"></a>Configurar a vida útil no Azure Cosmos DB

No Azure Cosmos DB, você pode optar por configurar TTL (vida útil) no nível do contêiner ou pode substituí-lo em um nível de item após a configuração do contêiner. Você pode configurar o TTL para um contêiner usando portal do Azure ou os SDKs específicos do idioma. As substituições de TTL no nível de item podem ser configuradas usando os SDKs.

## <a name="enable-time-to-live-on-a-container-using-azure-portal"></a>Habilitar a vida útil em um contêiner usando portal do Azure

Use as etapas a seguir para habilitar a vida útil em um contêiner sem expiração. Habilite essa permissão para permitir que o TTL seja substituído no nível do item. Você também pode definir o TTL inserindo um valor diferente de zero por segundos.

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

2. Crie uma nova conta do Azure Cosmos ou selecione uma conta existente.

3. Abra o painel de **Data Explorer** .

4. Selecione um contêiner existente, expanda-o e modifique os seguintes valores:

   * Abra a janela **dimensionar & configurações** .
   * Em **configuração** de localizar, **vida**útil.
   * Selecione **ativado (sem padrão)** ou selecione **ativado** e defina um valor de TTL
   * Clique em **Guardar** para guardar as alterações.

   ![Configurar a vida útil no portal do Azure](./media/how-to-time-to-live/how-to-time-to-live-portal.png)


- Quando DefaultTimeToLive for NULL, o tempo de vida estará desativado
- Quando DefaultTimeToLive for-1, a configuração de tempo de vida estará ativada (sem padrão)
- Quando DefaultTimeToLive tem qualquer outro valor int (exceto 0), a configuração de tempo de vida está ativada

## <a name="enable-time-to-live-on-a-container-using-sdk"></a>Habilitar a vida útil em um contêiner usando o SDK

### <a id="dotnet-enable-noexpiry"></a>SDK do .NET v2 (Microsoft. Azure. DocumentDB)

```csharp
// Create a new container with TTL enabled and without any expiration value
DocumentCollection collectionDefinition = new DocumentCollection();
collectionDefinition.Id = "myContainer";
collectionDefinition.PartitionKey.Paths.Add("/myPartitionKey");
collectionDefinition.DefaultTimeToLive = -1; //(never expire by default)

DocumentCollection ttlEnabledCollection = await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("myDatabaseName"),
    collectionDefinition);
```

### <a id="dotnet-enable-noexpiry"></a>SDK do .NET v3 (Microsoft. Azure. Cosmos)

```csharp
// Create a new container with TTL enabled and without any expiration value
await client.GetDatabase("database").CreateContainerAsync(new ContainerProperties
{
    Id = "container",
    PartitionKeyPath = "/myPartitionKey",
    DefaultTimeToLive = -1 //(never expire by default)
});
```

## <a name="set-time-to-live-on-a-container-using-sdk"></a>Definir a vida útil em um contêiner usando o SDK

Para definir a vida útil em um contêiner, você precisa fornecer um número positivo diferente de zero que indique o período de tempo em segundos. Com base no valor TTL configurado, todos os itens no contêiner após o último carimbo de data/hora `_ts` modificado do item são excluídos.

### <a id="dotnet-enable-withexpiry"></a>SDK do .NET v2 (Microsoft. Azure. DocumentDB)

```csharp
// Create a new container with TTL enabled and a 90 day expiration
DocumentCollection collectionDefinition = new DocumentCollection();
collectionDefinition.Id = "myContainer";
collectionDefinition.PartitionKey.Paths.Add("/myPartitionKey");
collectionDefinition.DefaultTimeToLive = 90 * 60 * 60 * 24; // expire all documents after 90 days

DocumentCollection ttlEnabledCollection = await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("myDatabaseName"),
    collectionDefinition;
```

### <a id="dotnet-enable-withexpiry"></a>SDK do .NET v3 (Microsoft. Azure. Cosmos)

```csharp
// Create a new container with TTL enabled and a 90 day expiration
await client.GetDatabase("database").CreateContainerAsync(new ContainerProperties
{
    Id = "container",
    PartitionKeyPath = "/myPartitionKey",
    DefaultTimeToLive = 90 * 60 * 60 * 24; // expire all documents after 90 days
});
```

### <a id="nodejs-enable-withexpiry"></a>SDK do NodeJS

```javascript
const containerDefinition = {
          id: "sample container1",
        };

async function createcontainerWithTTL(db: Database, containerDefinition: ContainerDefinition, collId: any, defaultTtl: number) {
      containerDefinition.id = collId;
      containerDefinition.defaultTtl = defaultTtl;
      await db.containers.create(containerDefinition);
}
```

## <a name="set-time-to-live-on-an-item"></a>Definir a vida útil em um item

Além de definir uma vida útil padrão em um contêiner, você pode definir uma vida útil para um item. Definir a vida útil no nível do item substituirá o TTL padrão do item nesse contêiner.

* Para definir o TTL em um item, você precisa fornecer um número positivo diferente de zero, que indica o período, em segundos, para expirar o item após o último carimbo de data/hora modificado `_ts`do item.

* Se o item não tiver um campo TTL, por padrão, o TTL definido para o contêiner será aplicado ao item.

* Se TTL for desabilitado no nível de contêiner, o campo TTL no item será ignorado até que TTL seja reabilitado no contêiner.

### <a id="portal-set-ttl-item"></a>Portal do Azure

Use as etapas a seguir para habilitar a vida útil em um item:

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

2. Crie uma nova conta do Azure Cosmos ou selecione uma conta existente.

3. Abra o painel de **Data Explorer** .

4. Selecione um contêiner existente, expanda-o e modifique os seguintes valores:

   * Abra a janela **dimensionar & configurações** .
   * Em **configuração** de localizar, **vida**útil.
   * Selecione **ativado (sem padrão)** ou selecione **ativado** e defina um valor TTL. 
   * Clique em **Guardar** para guardar as alterações.

5. Em seguida, navegue até o item para o qual você deseja definir a vida útil, `ttl` adicione a propriedade e selecione **Atualizar**. 

   ```json
   {
    "id": "1",
    "_rid": "Jic9ANWdO-EFAAAAAAAAAA==",
    "_self": "dbs/Jic9AA==/colls/Jic9ANWdO-E=/docs/Jic9ANWdO-EFAAAAAAAAAA==/",
    "_etag": "\"0d00b23f-0000-0000-0000-5c7712e80000\"",
    "_attachments": "attachments/",
    "ttl": 10,
    "_ts": 1551307496
   }
   ```

### <a id="dotnet-set-ttl-item"></a>SDK do .NET (qualquer)

```csharp
// Include a property that serializes to "ttl" in JSON
public class SalesOrder
{
    [JsonProperty(PropertyName = "id")]
    public string Id { get; set; }
    [JsonProperty(PropertyName="cid")]
    public string CustomerId { get; set; }
    // used to set expiration policy
    [JsonProperty(PropertyName = "ttl", NullValueHandling = NullValueHandling.Ignore)]
    public int? ttl { get; set; }

    //...
}
// Set the value to the expiration in seconds
SalesOrder salesOrder = new SalesOrder
{
    Id = "SO05",
    CustomerId = "CO18009186470",
    ttl = 60 * 60 * 24 * 30;  // Expire sales orders in 30 days
};
```

### <a id="nodejs-set-ttl-item"></a>SDK do NodeJS

```javascript
const itemDefinition = {
          id: "doc",
          name: "sample Item",
          key: "value", 
          ttl: 2
        };
```


## <a name="reset-time-to-live"></a>Redefinir vida útil

Você pode redefinir a vida útil em um item executando uma operação de gravação ou atualização no item. A operação de gravação ou atualização definirá `_ts` o como a hora atual, e o TTL do item a expirar começará novamente. Se desejar alterar a TTL de um item, você poderá atualizar o campo da mesma forma que atualizar qualquer outro campo.

### <a id="dotnet-extend-ttl-item"></a>SDK do .NET v2 (Microsoft. Azure. DocumentDB)

```csharp
// This examples leverages the Sales Order class above.
// Read a document, update its TTL, save it.
response = await client.ReadDocumentAsync(
    "/dbs/salesdb/colls/orders/docs/SO05"),
    new RequestOptions { PartitionKey = new PartitionKey("CO18009186470") });

Document readDocument = response.Resource;
readDocument.ttl = 60 * 30 * 30; // update time to live
response = await client.ReplaceDocumentAsync(readDocument);
```

### <a id="dotnet-extend-ttl-item"></a>SDK do .NET v3 (Microsoft. Azure. Cosmos)

```csharp
// This examples leverages the Sales Order class above.
// Read a document, update its TTL, save it.
ItemResponse<SalesOrder> itemResponse = await client.GetContainer("database", "container").ReadItemAsync<SalesOrder>("SO05", new PartitionKey("CO18009186470"));

itemResponse.Resource.ttl = 60 * 30 * 30; // update time to live
await client.GetContainer("database", "container").ReplaceItemAsync(itemResponse.Resource, "SO05");
```

## <a name="turn-off-time-to-live"></a>Desligar a vida útil

Se a vida útil tiver sido definida em um item e você não quiser mais que esse item expire, você poderá obter o item, remover o campo TTL e substituir o item no servidor. Quando o campo TTL é removido do item, o valor de TTL padrão atribuído ao contêiner é aplicado ao item. Defina o valor de TTL como-1 para impedir que um item expire e não herde o valor TTL do contêiner.

### <a id="dotnet-turn-off-ttl-item"></a>SDK do .NET v2 (Microsoft. Azure. DocumentDB)

```csharp
// This examples leverages the Sales Order class above.
// Read a document, turn off its override TTL, save it.
response = await client.ReadDocumentAsync(
    "/dbs/salesdb/colls/orders/docs/SO05"),
    new RequestOptions { PartitionKey = new PartitionKey("CO18009186470") });

Document readDocument = response.Resource;
readDocument.ttl = null; // inherit the default TTL of the container

response = await client.ReplaceDocumentAsync(readDocument);
```

### <a id="dotnet-turn-off-ttl-item"></a>SDK do .NET v3 (Microsoft. Azure. Cosmos)

```csharp
// This examples leverages the Sales Order class above.
// Read a document, turn off its override TTL, save it.
ItemResponse<SalesOrder> itemResponse = await client.GetContainer("database", "container").ReadItemAsync<SalesOrder>("SO05", new PartitionKey("CO18009186470"));

itemResponse.Resource.ttl = null; // inherit the default TTL of the container
await client.GetContainer("database", "container").ReplaceItemAsync(itemResponse.Resource, "SO05");
```

## <a name="disable-time-to-live"></a>Desabilitar vida útil

Para desabilitar a vida útil em um contêiner e impedir que o processo em segundo plano Verifique se há itens `DefaultTimeToLive` expirados, a propriedade no contêiner deve ser excluída. A exclusão dessa propriedade é diferente de defini-la como-1. Quando você o define como-1, novos itens adicionados ao contêiner residirão para sempre, no entanto, você pode substituir esse valor em itens específicos no contêiner. Quando você remove a propriedade TTL do contêiner, os itens nunca expirarão, mesmo que tenham substituído explicitamente o valor TTL padrão anterior.

### <a id="dotnet-disable-ttl"></a>SDK do .NET v2 (Microsoft. Azure. DocumentDB)

```csharp
// Get the container, update DefaultTimeToLive to null
DocumentCollection collection = await client.ReadDocumentCollectionAsync("/dbs/salesdb/colls/orders");
// Disable TTL
collection.DefaultTimeToLive = null;
await client.ReplaceDocumentCollectionAsync(collection);
```

### <a id="dotnet-disable-ttl"></a>SDK do .NET v3 (Microsoft. Azure. Cosmos)

```csharp
// Get the container, update DefaultTimeToLive to null
ContainerResponse containerResponse = await client.GetContainer("database", "container").ReadContainerAsync();
// Disable TTL
containerResponse.Resource.DefaultTimeToLive = null;
await client.GetContainer("database", "container").ReplaceContainerAsync(containerResponse.Resource);
```

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre o tempo de vida no seguinte artigo:

* [Vida útil](time-to-live.md)
