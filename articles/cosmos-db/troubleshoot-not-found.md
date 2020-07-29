---
title: Resolução de problemas Azure Cosmos DB não encontrou exceção
description: Como diagnosticar e corrigir não encontrados exceção
author: j82w
ms.service: cosmos-db
ms.date: 07/13/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: f391772672904a1e2bdfa0a741d9f85a6edeea8b
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87294619"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-not-found"></a>Diagnóstico e resolução de problemas Azure Cosmos DB não encontrado
O código de estado HTTP 404 representa que o recurso já não existe.

## <a name="expected-behavior"></a>Comportamento esperado
Existem muitos cenários válidos onde uma aplicação está à espera de um 404, e corretamente lida com o cenário.

## <a name="not-found-was-returned-for-an-item-that-should-exist-or-does-exist"></a>Não encontrado foi devolvido para um item que deve existir ou existe
Abaixo estão os possíveis motivos para que um código de estado 404 seja devolvido se o item deve ou sai.

### <a name="1-race-condition"></a>1. Condição de corrida
Existem vários casos de clientes SDK e a leitura aconteceu antes da escrita.

#### <a name="solution"></a>Solução:
1. A consistência da conta padrão para Cosmos DB é a consistência da sessão. Quando um item é criado ou atualizado, a resposta devolverá um token de sessão que pode ser passado entre instâncias SDK para garantir que o pedido de leitura é lido a partir de uma réplica com essa alteração.
2. Mude o [nível de consistência](consistency-levels-choosing.md) para um [nível mais forte](consistency-levels-tradeoffs.md)

### <a name="2-invalid-partition-key-and-id-combination"></a>2. Chave de partição inválida e combinação de identificação
A chave de partição e a combinação de ID não são válidas.

#### <a name="solution"></a>Solução:
Corrija a lógica de aplicação que está a causar a combinação incorreta. 

### <a name="3-invalid-character-in-item-id"></a>3. Caráter inválido no iD do item
Um item é inserido em Cosmos DB com um [carácter inválido](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.resource.id?view=azure-dotnet#remarks) no ID do item.

#### <a name="solution"></a>Solução:
Recomenda-se que os utilizadores alterem o ID para um valor diferente que não contenha os caracteres especiais. Se alterar o ID não é uma opção, pode codificar o ID para escapar aos caracteres especiais.

Os itens já inseridos no recipiente o ID podem ser substituídos utilizando valores RID em vez de referências baseadas em nomes.
```c#
// Get a container reference that use RID values
ContainerProperties containerProperties = await this.Container.ReadContainerAsync();
string[] selfLinkSegments = containerProperties.SelfLink.Split('/');
string databaseRid = selfLinkSegments[1];
string containerRid = selfLinkSegments[3];
Container containerByRid = this.cosmosClient.GetContainer(databaseRid, containerRid);

// List of invalid characters are listed here.
//https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.resource.id?view=azure-dotnet#remarks
FeedIterator<JObject> invalidItemsIterator = this.Container.GetItemQueryIterator<JObject>(
    @"select * from t where CONTAINS(t.id, ""/"") or CONTAINS(t.id, ""#"") or CONTAINS(t.id, ""?"") or CONTAINS(t.id, ""\\"") ");
while (invalidItemsIterator.HasMoreResults)
{
    foreach (JObject itemWithInvalidId in await invalidItemsIterator.ReadNextAsync())
    {
        // It recommend to chose a new ID that does not contain special characters, but
        // if that is not possible then it can be Base64 encoded to escape the special characters
        byte[] plainTextBytes = Encoding.UTF8.GetBytes(itemWithInvalidId["id"].ToString());
        itemWithInvalidId["id"] = Convert.ToBase64String(plainTextBytes);

        // Update the item with the new ID value using the rid based container reference
        JObject item = await containerByRid.ReplaceItemAsync<JObject>(
            item: itemWithInvalidId,
            ID: itemWithInvalidId["_rid"].ToString(),
            partitionKey: new Cosmos.PartitionKey(itemWithInvalidId["status"].ToString()));

        // Validate the new ID can be read using the original name based contianer reference
        await this.Container.ReadItemAsync<ToDoActivity>(
            item["id"].ToString(),
            new Cosmos.PartitionKey(item["status"].ToString())); ;
    }
}
```

### <a name="4-time-to-live-ttl-purge"></a>4. Tempo para viver (TTL) purga
O item tinha o conjunto de propriedades [Time To Live (TTL).](https://docs.microsoft.com/azure/cosmos-db/time-to-live) O item foi purgado porque o tempo de viver tinha expirado.

#### <a name="solution"></a>Solução:
Altere o tempo para viver para evitar que o item sejam purgados.

### <a name="5-lazy-indexing"></a>5. Indexação preguiçosa
A [indexação preguiçosa](index-policy.md#indexing-mode) não alcançou.

#### <a name="solution"></a>Solução:
Aguarde que o indexante acompanhe ou altere a política de indexação

### <a name="6-parent-resource-deleted"></a>6. Recurso dos pais eliminado
A base de dados e/ou o contentor em que o artigo existe foi eliminado.

#### <a name="solution"></a>Solução:
1. [Restaurar](https://docs.microsoft.com/azure/cosmos-db/online-backup-and-restore#backup-retention-period) o recurso principal ou recriar os recursos.
2. Criar um novo recurso para substituir o recurso eliminado

## <a name="next-steps"></a>Passos seguintes
* [Diagnosticar e resolver problemas](troubleshoot-dot-net-sdk.md) ao utilizar a Azure Cosmos DB .NET SDK
* Conheça as diretrizes de desempenho para [.NET V3](performance-tips-dotnet-sdk-v3-sql.md) e [.NET V2](performance-tips.md)