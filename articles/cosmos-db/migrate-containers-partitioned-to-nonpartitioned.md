---
title: Migrar contentores Azure Cosmos não divididos para contentores divididos
description: Aprenda a migrar todos os recipientes não divididos existentes em recipientes divididos.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/25/2019
ms.author: mjbrown
ms.openlocfilehash: 742ef62895f3ef64e8fa22ab21d2947bee57776b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77623353"
---
# <a name="migrate-non-partitioned-containers-to-partitioned-containers"></a>Migrar contentores não divididos para contentores divididos

A Azure Cosmos DB suporta a criação de contentores sem chave de partição. Atualmente pode criar recipientes não divididos utilizando SDKS DB Azure CLI e Azure Cosmos (.Net, Java, NodeJs) que têm uma versão inferior ou igual a 2.x. Não é possível criar recipientes não divididos utilizando o portal Azure. No entanto, estes recipientes não divididos não são elásticos e têm capacidade fixa de armazenamento de 20 GB e limite de entrada de 10K RU/s.

Os recipientes não divididos são legados e deve migrar os seus recipientes não divididos existentes para recipientes divididos para escalar o armazenamento e a entrada. A Azure Cosmos DB fornece um mecanismo definido para migrar os seus recipientes não divididos para recipientes divididos. Este documento explica como todos os recipientes não divididos existentes são migrados automaticamente para recipientes divididos. Só pode usufruir da funcionalidade de migração automática se estiver a utilizar a versão V3 dos SDKs em todos os idiomas.

> [!NOTE]
> Atualmente, não é possível migrar as contas da Azure Cosmos DB MongoDB e da API Gremlin utilizando os passos descritos neste documento.

## <a name="migrate-container-using-the-system-defined-partition-key"></a>Migrar recipiente utilizando a chave de partição definida pelo sistema

Para apoiar a migração, a Azure Cosmos `/_partitionkey` DB fornece uma chave de partição definida pelo sistema, nomeada em todos os recipientes que não têm uma chave de partição. Não é possível alterar a definição da chave da divisória após a migração dos contentores. Por exemplo, a definição de um recipiente migrado para um recipiente dividido será a seguinte:

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

Depois de o contentor ser migrado, pode `_partitionKey` criar documentos povoando a propriedade juntamente com as outras propriedades do documento. A `_partitionKey` propriedade representa a chave de partilha dos seus documentos.

A escolha da chave de partição certa é importante para utilizar a entrada provisionada da melhor forma. Para mais informações, consulte como escolher um artigo [chave de partilha.](partitioning-overview.md)

> [!NOTE]
> Só pode tirar partido da chave de partição definida pelo sistema se estiver a utilizar a versão mais recente/V3 dos SDKs em todos os idiomas.

O exemplo seguinte mostra um código de amostra para criar um documento com a chave de partição definida pelo sistema e ler esse documento:

**Representação json do documento**

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

    [JsonProperty(PropertyName = "_partitionKey", NullValueHandling = NullValueHandling.Ignore)]
    public string PartitionKey {get {return this.DeviceId; set; }
}

CosmosContainer migratedContainer = database.Containers["testContainer"];

DeviceInformationItem deviceItem = new DeviceInformationItem() {
  Id = "1234",
  DeviceId = "3cf4c52d-cc67-4bb8-b02f-f6185007a808"
}

ItemResponse<DeviceInformationItem > response = 
  await migratedContainer.CreateItemAsync<DeviceInformationItem>(
    deviceItem.PartitionKey, 
    deviceItem
  );

// Read back the document providing the same partition key
ItemResponse<DeviceInformationItem> readResponse = 
  await migratedContainer.ReadItemAsync<DeviceInformationItem>( 
    partitionKey:deviceItem.PartitionKey, 
    id: device.Id
  );

```

Para obter a amostra completa, consulte o repositório GitHub de [amostras .Net.][1]
                      
## <a name="migrate-the-documents"></a>Migrar os documentos

Enquanto a definição do recipiente é melhorada com uma propriedade chave de divisória, os documentos dentro do recipiente não são migrados automaticamente. O que significa que `/_partitionKey` o caminho da propriedade chave de divisória do sistema não é adicionado automaticamente aos documentos existentes. É necessário reparticionar os documentos existentes lendo os documentos `_partitionKey` que foram criados sem chave de partição e reescrevê-los com propriedade nos documentos.

## <a name="access-documents-that-dont-have-a-partition-key"></a>Documentos de acesso que não têm chave de partição

As aplicações podem aceder aos documentos existentes que não têm uma chave de partição utilizando a propriedade especial do sistema chamada "PartitionKey.None", este é o valor dos documentos não migrados. Você pode usar esta propriedade em todas as operações crud e consulta. O exemplo seguinte mostra uma amostra para ler um único documento da Tecla NonePartitionKey. 

```csharp
CosmosItemResponse<DeviceInformationItem> readResponse = 
await migratedContainer.Items.ReadItemAsync<DeviceInformationItem>( 
  partitionKey: PartitionKey.None, 
  id: device.Id
); 

```

Para obter a amostra completa sobre como repartição dos documentos, consulte o repositório [de amostras .Net][1] GitHub. 

## <a name="compatibility-with-sdks"></a>Compatibilidade com SDKs

A versão mais antiga de SDKs Db Azurs Cosmos, tais como V2.x.x e V1.x.x, não suportam a propriedade chave de divisória definida pelo sistema. Assim, quando você lê a definição do recipiente de um SDK mais antigo, ele não contém qualquer definição de chave de divisória e estes recipientes se comportarão exatamente como antes. As aplicações que são construídas com a versão mais antiga dos SDKs continuam a funcionar com não divisórias, como é o caso de alterações. 

Se um recipiente migrado for consumido pela versão mais recente/V3 do SDK e começar a povoar a chave de partição definida pelo sistema dentro dos novos documentos, não pode aceder mais (ler, atualizar, excluir, consultar) tais documentos dos SDKs mais antigos.

## <a name="known-issues"></a>Problemas conhecidos

**A consulta para a contagem de itens que foram inseridos sem chave de partição utilizando V3 SDK pode envolver um maior consumo de entrada**

Se consultar o V3 SDK pelos itens que são inseridos utilizando V2 SDK, ou os itens inseridos utilizando `PartitionKey.None` o V3 SDK com parâmetro, `PartitionKey.None` a consulta de contagem pode consumir mais RU/s se o parâmetro for fornecido nas FeedOptions. Recomendamos que não forneça `PartitionKey.None` o parâmetro se não forem inseridos outros itens com uma chave de partição.

Se os novos itens forem inseridos com valores diferentes para a chave de `FeedOptions` partição, a consulta para tais contagens de item, passando a chave adequada, não terá quaisquer problemas. Depois de inserir novos documentos com chave de partição, se precisar de consultar apenas a contagem de documentos sem o valor-chave da partilha, essa consulta pode voltar a incorrer em RU/s mais elevados semelhantes às coleções regulares de partição.

## <a name="next-steps"></a>Passos seguintes

* [Criação de partições no Azure Cosmos DB](partitioning-overview.md)
* [Request Units in Azure Cosmos DB](request-units.md) (Unidades de Pedido no Azure Cosmos DB)
* [Aprovisionar o débito em contentores e bases de dados](set-throughput.md)
* [Trabalhar com uma conta do Azure Cosmos](account-overview.md)

[1]: https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/NonPartitionContainerMigration