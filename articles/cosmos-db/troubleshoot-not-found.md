---
title: Resolução de problemas Azure Cosmos DB não encontrou exceções
description: Saiba como diagnosticar e corrigir não encontrar exceções.
author: j82w
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.date: 07/13/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 22cce2c620d23ab477de5d92bb8c6d4f5ef5a493
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2021
ms.locfileid: "102425129"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-not-found-exceptions"></a>Diagnosticar e resolver problemas Azure Cosmos DB não encontrou exceções
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

O código de estado HTTP 404 indica que o recurso já não existe.

## <a name="expected-behavior"></a>Comportamento esperado
Existem muitos cenários válidos onde uma aplicação espera um código 404 e lida corretamente com o cenário.

## <a name="a-not-found-exception-was-returned-for-an-item-that-should-exist-or-does-exist"></a>Uma exceção não encontrada foi devolvida para um item que deveria existir ou existe
Aqui estão as possíveis razões para que um código de estado 404 seja devolvido se o item deve existir ou existir.

### <a name="the-read-session-is-not-available-for-the-input-session-token"></a>A sessão de leitura não está disponível para o token de sessão de entrada

#### <a name="solution"></a>Solução:
1. Atualize o seu SDK atual para a versão mais recente disponível. As causas mais comuns para este erro em particular foram corrigidas nas versões SDK mais recentes.

### <a name="race-condition"></a>Condição race
Existem vários casos de clientes SDK e a leitura aconteceu antes da escrita.

#### <a name="solution"></a>Solução:
1. A consistência da conta padrão para Azure Cosmos DB é a consistência da sessão. Quando um item é criado ou atualizado, a resposta devolve um token de sessão que pode ser passado entre instâncias SDK para garantir que o pedido de leitura é lido a partir de uma réplica com essa alteração.
1. Mude o [nível de consistência](./consistency-levels.md) para um [nível mais forte](./consistency-levels.md).

### <a name="invalid-partition-key-and-id-combination"></a>Chave de partição inválida e combinação de ID
A chave de partição e a combinação de identificação não são válidas.

#### <a name="solution"></a>Solução:
Corrija a lógica de aplicação que está a causar a combinação incorreta. 

### <a name="invalid-character-in-an-item-id"></a>Personagem inválido em um iD item
Um item é inserido na Azure Cosmos DB com um [carácter inválido](/dotnet/api/microsoft.azure.documents.resource.id#remarks) no ID do item.

#### <a name="solution"></a>Solução:
Mude o ID para um valor diferente que não contenha os caracteres especiais. Se alterar o ID não é uma opção, pode codificar o ID para escapar aos caracteres especiais. A Base64 ainda pode produzir um nome com um caracteres inválidos '/' que precisa de ser substituído.

Os artigos já inseridos no recipiente para o ID podem ser substituídos utilizando valores RID em vez de referências baseadas em nomes.
```c#
// Get a container reference that uses RID values.
ContainerProperties containerProperties = await this.Container.ReadContainerAsync();
string[] selfLinkSegments = containerProperties.SelfLink.Split('/');
string databaseRid = selfLinkSegments[1];
string containerRid = selfLinkSegments[3];
Container containerByRid = this.cosmosClient.GetContainer(databaseRid, containerRid);

// Invalid characters are listed here.
//https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.resource.id#remarks
FeedIterator<JObject> invalidItemsIterator = this.Container.GetItemQueryIterator<JObject>(
    @"select * from t where CONTAINS(t.id, ""/"") or CONTAINS(t.id, ""#"") or CONTAINS(t.id, ""?"") or CONTAINS(t.id, ""\\"") ");
while (invalidItemsIterator.HasMoreResults)
{
    foreach (JObject itemWithInvalidId in await invalidItemsIterator.ReadNextAsync())
    {
        // Choose a new ID that doesn't contain special characters.
        // If that isn't possible, then Base64 encode the ID to escape the special characters.
        byte[] plainTextBytes = Encoding.UTF8.GetBytes(itemWithInvalidId["id"].ToString());
        itemWithInvalidId["id"] = Convert.ToBase64String(plainTextBytes).Replace('/', '!');

        // Update the item with the new ID value by using the RID-based container reference.
        JObject item = await containerByRid.ReplaceItemAsync<JObject>(
            item: itemWithInvalidId,
            ID: itemWithInvalidId["_rid"].ToString(),
            partitionKey: new Cosmos.PartitionKey(itemWithInvalidId["status"].ToString()));

        // Validating the new ID can be read by using the original name-based container reference.
        await this.Container.ReadItemAsync<ToDoActivity>(
            item["id"].ToString(),
            new Cosmos.PartitionKey(item["status"].ToString())); ;
    }
}
```

### <a name="time-to-live-purge"></a>Tempo para viver purga
O item tinha o conjunto de propriedades [Time to Live (TTL).](./time-to-live.md) O artigo foi purgado porque a propriedade TTL expirou.

#### <a name="solution"></a>Solução:
Altere a propriedade TTL para evitar que o item seja purgado.

### <a name="lazy-indexing"></a>Indexação preguiçosa
A [indexação preguiçosa](index-policy.md#indexing-mode) não alcançou.

#### <a name="solution"></a>Solução:
Aguarde que o indexante acompanhe ou altere a política de indexação.

### <a name="parent-resource-deleted"></a>Recurso dos pais eliminado
A base de dados ou o contentor em que o artigo existe foi eliminado.

#### <a name="solution"></a>Solução:
1. [Restaurar](./configure-periodic-backup-restore.md#request-restore) o recurso principal, ou recriar os recursos.
1. Crie um novo recurso para substituir o recurso eliminado.

### <a name="7-containercollection-names-are-case-sensitive"></a>7. Os nomes dos contentores/recolha são sensíveis aos casos
Os nomes de contentores/coleções são sensíveis a casos na Cosmos DB.

#### <a name="solution"></a>Solução:
Certifique-se de que utiliza o nome exato durante a ligação ao Cosmos DB.

## <a name="next-steps"></a>Passos seguintes
* [Diagnosticar e resolver problemas](troubleshoot-dot-net-sdk.md) quando utilizar o Azure Cosmos DB .NET SDK.
* Saiba mais sobre as diretrizes de desempenho para [.NET v3](performance-tips-dotnet-sdk-v3-sql.md) e [.NET v2](performance-tips.md).
* [Diagnosticar e resolver problemas](troubleshoot-java-sdk-v4-sql.md) quando utilizar o Azure Cosmos DB Java v4 SDK.
* Saiba mais sobre as diretrizes de desempenho para [Java v4 SDK](performance-tips-java-sdk-v4-sql.md).
