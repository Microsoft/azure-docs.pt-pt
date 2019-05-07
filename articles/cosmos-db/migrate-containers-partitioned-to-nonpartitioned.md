---
title: Migrar não-particionada contentores do Azure Cosmos DB para contentores particionadas
description: Saiba como migrar todos os existentes não-particionada contentores em contentores particionadas.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: mjbrown
ms.openlocfilehash: e8aaee8cb7df794652b2c560c5250921c1a4e060
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65160639"
---
# <a name="migrate-non-partitioned-containers-to-partitioned-containers"></a>Migrar os contentores não-particionada para contentores particionadas

O Azure Cosmos DB suporta a criação de contentores sem uma chave de partição. Atualmente pode criar contentores de não-particionada através da utilização da CLI do Azure e SDKs do Azure Cosmos DB (.Net, Java, NodeJs) que têm uma versão inferior ou igual a 2.x. Não é possível criar contentores de não-particionada com o portal do Azure. No entanto, esses contentores de não-particionada não são elásticos e resolvemos a capacidade de armazenamento de limite de 10 GB e débito de 10 mil RU/s.

Os contentores não-particionada são herdados e deve migrar os contentores não-particionada existentes particionada contentores no armazenamento de escala e taxa de transferência. O Azure Cosmos DB fornece um mecanismo de sistema definido para migrar os seus contentores não-particionada para contentores particionadas. Este documento explica como todos os existentes não-particionada contentores são migrados automaticamente em contentores particionadas. Pode aproveitar a funcionalidade de migração automática apenas se estiver a utilizar a versão de V3 SDKs em todos os idiomas.

> [!NOTE] 
> Atualmente, não é possível migrar as contas do Azure Cosmos DB MongoDB e Gremlin API, utilizando os passos descritos neste documento. 

## <a name="migrate-container-using-the-system-defined-partition-key"></a>Migrar o contentor com a chave de partição definido pelo sistema

Para suportar a migração, o Azure Cosmos DB define uma chave de partição de sistema definido com o nome `/_partitionkey` em todos os contentores que não têm uma chave de partição. Não é possível alterar a definição da chave de partição depois dos contentores são migrados. Por exemplo, a definição de um contentor que é migrado para um contentor particionado será da seguinte forma: 

```json
{
    "Id": "CollId" 
  "partitionKey": {
    "paths": [
      "/_partitionKey"
    ],
    "kind": "Hash"
  },
}
```
 
Depois do contentor é migrado, pode criar documentos preenchendo o `_partitionKey` propriedade juntamente com as outras propriedades do documento. O `_partitionKey` propriedade representa a chave de partição dos seus documentos. 

Escolhendo a chave de partição correta é importante utilizar o débito aprovisionado de forma otimizada. Para obter mais informações, consulte [como escolher uma chave de partição](partitioning-overview.md) artigo. 

> [!NOTE]
> Pode tirar partido da chave de partição definida de sistema apenas se estiver a utilizar a versão mais recente/V3 SDKs em todos os idiomas.

O exemplo seguinte mostra um código de exemplo para criar um documento com a chave de partição definida de sistema e ler esse documento:

**Representação JSON do documento**

```csharp
DeviceInformationItem = new DeviceInformationItem
{
   "id": "elevator/PugetSound/Building44/Floor1/1",
   "deviceId": "3cf4c52d-cc67-4bb8-b02f-f6185007a808",
   "_partitionKey": "3cf4c52d-cc67-4bb8-b02f-f6185007a808"
} 

public class DeviceInformationItem
{
    [JsonProperty(PropertyName = "id")]
    public string Id { get; set; }

    [JsonProperty(PropertyName = "deviceId")]
    public string DeviceId { get; set; }

    [JsonProperty(PropertyName = "_partitionKey")]
    public string PartitionKey {get {return this.DeviceId; set; }
}

CosmosContainer migratedContainer = database.Containers["testContainer"];

DeviceInformationItem deviceItem = new DeviceInformationItem() {
  Id = "1234", 
  DeviceId = "3cf4c52d-cc67-4bb8-b02f-f6185007a808"
} 

CosmosItemResponse<DeviceInformationItem > response = 
  await migratedContainer.Items.CreateItemAsync(
    deviceItem.PartitionKey, 
    deviceItem
  );

// Read back the document providing the same partition key
CosmosItemResponse<DeviceInformationItem> readResponse = 
  await migratedContainer.Items.ReadItemAsync<DeviceInformationItem>( 
    partitionKey:deviceItem.PartitionKey, 
    id: device.Id
  ); 

```

Para o exemplo completo, consulte a [amostras do .net](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/CodeSamples) repositório do GitHub. 
                      
## <a name="migrate-the-documents"></a>Migrar os documentos

Embora a definição de contentor foi melhorada com uma propriedade de chave de partição, os documentos dentro do contentor não são automaticamente migradas. O que significa a propriedade de chave de partição de sistema `/_partitionKey` caminho não é automaticamente adicionado aos documentos existentes. Tem de criar novas partições os documentos existentes ao ler os documentos que foram criados sem uma chave de partição e reescrevê-las com `_partitionKey` propriedade nos documentos. 

## <a name="access-documents-that-dont-have-a-partition-key"></a>Documentos de acesso que não tem uma chave de partição

As aplicações podem aceder os documentos existentes que não têm uma chave de partição, utilizando a propriedade especial do sistema chamada "CosmosContainerSettings.NonePartitionKeyValue", este é o valor dos documentos não migrados. Pode utilizar esta propriedade em todas as operações CRUD e de consulta. O exemplo seguinte mostra um exemplo para ler um documento único do NonePartitionKey. 

```csharp
CosmosItemResponse<DeviceInformationItem> readResponse = 
await migratedContainer.Items.ReadItemAsync<DeviceInformationItem>( 
  partitionKey: CosmosContainerSettings.NonePartitionKeyValue, 
  id: device.Id
); 

```

Para o exemplo completo sobre como criar novas partições de documentos, consulte a [amostras do .net](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/CodeSamples) repositório do GitHub. 

## <a name="compatibility-with-sdks"></a>Compatibilidade com os SDKs

A versão mais antiga de SDKs do Azure Cosmos DB como V2.x.x e V1.x.x não suporta a propriedade de chave de partição definido pelo sistema. Então, quando ler a definição de contentor a partir de um SDK mais antigo, ele não contém qualquer definição de chave de partição e estes contentores irão comportar-se exatamente como antes. Aplicativos que são criados com a versão mais antiga dos SDKs continuam a trabalhar com não-particionada como está sem quaisquer alterações. 

Se um contentor migrado é consumido pela versão mais recente/V3 do SDK e começar a preencher a chave de partição de sistema definido dentro dos documentos novos, não pode aceder (read, update, delete, consulta), tais documentos nos SDKs mais antigos mais.

## <a name="next-steps"></a>Passos Seguintes

* [Criação de partições no Azure Cosmos DB](partitioning-overview.md)
* [Unidades de pedido no Azure Cosmos DB](request-units.md)
* [Débito de aprovisionar em contentores e bases de dados](set-throughput.md)
* [Trabalhar com a conta do Cosmos do Azure](account-overview.md)