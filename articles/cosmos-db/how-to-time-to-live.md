---
title: Saiba como configurar e gerir o TTL no Azure Cosmos DB
description: Saiba como configurar e gerir o tempo de duração no Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 11/14/2018
ms.author: mjbrown
ms.openlocfilehash: 25824b9959a4fef536ca9fe247f29fc9aaece1f7
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2018
ms.locfileid: "51626500"
---
# <a name="how-to-configure-time-to-live-in-azure-cosmos-db"></a>Como configurar o TTL no Azure Cosmos DB

No Azure Cosmos DB, pode optar por configurar o tempo Live (TTL) ao nível do contentor ou pode substituí-la num nível de item após a definição do contentor. Pode configurar o TTL para um contentor com o portal do Azure ou os SDKs específicos de idiomas. Substituições TTL ao nível do item podem ser configuradas através de SDKs.

## <a name="enable-time-to-live-on-a-container-using-azure-portal"></a>Ativar o TTL num contentor com o portal do Azure

Utilize os seguintes passos para ativar o TTL num contêiner sem expiração. Ative esta opção para permitir que o valor de TTL ser substituído ao nível do item. Também pode definir o valor de TTL ao introduzir um valor diferente de zero para segundos.

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

2. Criar uma nova conta do Cosmos do Azure ou selecione uma conta existente.

3. Abra o **Data Explorer** painel.

4. Selecione um contentor existente, expandi-lo e modificar os seguintes valores:

   * Abra o **dimensionamento e definições** janela.
   * Sob **definição** localizar, **TTL**.
   * Selecione **ativada (sem predefinição)** ou selecione **no** e definir um valor TTL
   * Clique em **Guardar** para guardar as alterações.

   ![Configurar o TTL no portal do Azure](./media/how-to-time-to-live/how-to-time-to-live-portal.png)

## <a name="enable-time-to-live-on-a-container-using-sdk"></a>Ativar o TTL num contentor com o SDK

### <a id="dotnet-enable-noexpiry"></a>SDK do .NET

```csharp
// Create a new collection with TTL enabled and without any expiration value
DocumentCollection collectionDefinition = new DocumentCollection();
collectionDefinition.Id = "myContainer";
collectionDefinition.PartitionKey.Paths.Add("/myPartitionKey");
collectionDefinition.DefaultTimeToLive = -1; //(never expire by default)

DocumentCollection ttlEnabledCollection = await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("myDatabaseName"),
    collectionDefinition,
    new RequestOptions { OfferThroughput = 20000 });
```

## <a name="set-time-to-live-on-a-container-using-sdk"></a>Definir o TTL num contentor com o SDK

### <a id="dotnet-enable-withexpiry"></a>SDK do .NET

Para definir o tempo de duração num contêiner, terá de fornecer um número positivo diferente de zero que indica o período de tempo em segundos. Com base no valor TTL configurado, todos os itens no contentor após a última modificação timestamp do item de `_ts` são eliminados.

```csharp
// Create a new collection with TTL enabled and a 90 day expiration
DocumentCollection collectionDefinition = new DocumentCollection();
collectionDefinition.Id = "myContainer";
collectionDefinition.PartitionKey.Paths.Add("/myPartitionKey");
collectionDefinition.DefaultTimeToLive = 90 * 60 * 60 * 24; // expire all documents after 90 days

DocumentCollection ttlEnabledCollection = await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("myDatabaseName"),
    collectionDefinition,
    new RequestOptions { OfferThroughput = 20000 });
```

## <a name="set-time-to-live-on-an-item"></a>Definir o tempo de duração num item

Além de definir um tempo de duração num contentor predefinido, pode definir um tempo de duração de um item. TTL ao nível do item de configuração irá substituir a predefinição do TTL do item nesse contentor.

* Para definir o valor de TTL num item, tem de fornecer um número positivo diferente de zero, que indica o período de, em segundos, para expirar o item após a última modificação timestamp do item de `_ts`.

* Se o item não tiver um campo de valor de TTL, em seguida, por predefinição, o valor de TTL definido para o contentor irá aplicar ao item.

* Se o valor de TTL está desativada no nível do contentor, o campo de valor de TTL no item será ignorado enquanto o TTL é novamente ativado no contentor.

### <a id="dotnet-set-ttl-item"></a>SDK do .NET

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
    public int? TimeToLive { get; set; }

    //...
}
// Set the value to the expiration in seconds
SalesOrder salesOrder = new SalesOrder
{
    Id = "SO05",
    CustomerId = "CO18009186470",
    TimeToLive = 60 * 60 * 24 * 30;  // Expire sales orders in 30 days
};
```

## <a name="reset-time-to-live"></a>Repor o TTL

Pode repor a hora em direto de um item, efetuando uma gravação ou atualizar a operação no item. A operação de escrita ou de atualização irá definir o `_ts` para a hora atual, e o valor de TTL para o item para expirar começarão de novo. Se pretender alterar o TTL de um item, pode atualizar o campo apenas quando atualiza qualquer outro campo.

### <a id="dotnet-extend-ttl-item"></a>SDK do .NET

```csharp
// This examples leverages the Sales Order class above.
// Read a document, update its TTL, save it.
response = await client.ReadDocumentAsync(
    "/dbs/salesdb/colls/orders/docs/SO05"),
    new RequestOptions { PartitionKey = new PartitionKey("CO18009186470") });

Document readDocument = response.Resource;
readDocument.TimeToLive = 60 * 30 * 30; // update time to live
response = await client.ReplaceDocumentAsync(readDocument);
```

## <a name="turn-off-time-to-live"></a>Desativar o TTL

Se o tempo de duração foi definido num item e que já não quer esse item para expirar, em seguida, pode obter o item, remover o campo de valor de TTL e substituir o item no servidor. Quando o campo de valor de TTL é removido do item, o valor TTL predefinido atribuído para o contentor é aplicado ao item. Defina o valor TTL como -1 para impedir que um item de expirar e não herdam o valor de TTL do contêiner.

### <a id="dotnet-turn-off-ttl-item"></a>SDK do .NET

```csharp
// This examples leverages the Sales Order class above.
// Read a document, turn off its override TTL, save it.
response = await client.ReadDocumentAsync(
    "/dbs/salesdb/colls/orders/docs/SO05"),
    new RequestOptions { PartitionKey = new PartitionKey("CO18009186470") });

Document readDocument = response.Resource;
readDocument.TimeToLive = null; // inherit the default TTL of the collection

response = await client.ReplaceDocumentAsync(readDocument);
```

## <a name="disable-time-to-live"></a>Desativar o TTL

Para desativar o tempo em direto de um contentor e parar o processo em segundo plano da verificação para itens expiradas, o `DefaultTimeToLive` propriedade no contentor deve ser eliminada. A eliminar esta propriedade é diferente de defini-la como -1. Ao definir como -1, novos itens adicionados ao contêiner irão durar para sempre, no entanto, pode substituir este valor em itens específicos no contentor. Ao remover a propriedade TTL do contêiner os itens irão expirar, mesmo se existirem que explicitamente que eles tenham substituído o valor TTL predefinido anterior.

### <a id="dotnet-disable-ttl"></a>SDK do .NET

```csharp
// Get the collection, update DefaultTimeToLive to null
DocumentCollection collection = await client.ReadDocumentCollectionAsync("/dbs/salesdb/colls/orders");
// Disable TTL
collection.DefaultTimeToLive = null;
await client.ReplaceDocumentCollectionAsync(collection);
```

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre o tempo de duração no seguinte artigo:

* [TTL](time-to-live.md)