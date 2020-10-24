---
title: Migrar recipientes Azure Cosmos não divididos para recipientes divididos
description: Aprenda a migrar todos os recipientes não divididos existentes para recipientes divididos.
author: markjbrown
ms.service: cosmos-db
ms.topic: how-to
ms.date: 09/25/2019
ms.author: mjbrown
ms.custom: devx-track-csharp
ms.openlocfilehash: 01e61abacac63d698456c3ab25826f9799d2837e
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92482286"
---
# <a name="migrate-non-partitioned-containers-to-partitioned-containers"></a>Migrar recipientes não divididos para recipientes divididos

A Azure Cosmos DB suporta a criação de recipientes sem uma chave de partição. Atualmente pode criar recipientes não divididos utilizando Azure CLI e Azure Cosmos DB SDKs (.Net, Java, NodeJs) que têm uma versão inferior ou igual a 2,x. Não é possível criar recipientes não divididos utilizando o portal Azure. No entanto, estes recipientes não divididos não são elásticos e têm uma capacidade de armazenamento fixa de 20 GB e limite de produção de 10K RU/s.

Os recipientes não divididos são legados e deve migrar os recipientes não divididos existentes para recipientes divididos para dimensionar o armazenamento e a produção. A Azure Cosmos DB fornece um mecanismo definido para migrar os seus recipientes não divididos para recipientes divididos. Este documento explica como todos os recipientes não divididos existentes são migrados automaticamente para recipientes divididos. Só pode tirar partido da funcionalidade de migração automática se estiver a utilizar a versão V3 de SDKs em todos os idiomas.

> [!NOTE]
> Atualmente, não é possível migrar as contas da Azure Cosmos DB MongoDB e da API gremlin utilizando os passos descritos neste documento.

## <a name="migrate-container-using-the-system-defined-partition-key"></a>Migrar o recipiente utilizando a chave de partição definida pelo sistema

Para apoiar a migração, a Azure Cosmos DB fornece uma chave de partição definida no sistema nomeada `/_partitionkey` em todos os recipientes que não têm uma chave de partição. Não é possível alterar a definição da chave de partição após a migração dos recipientes. Por exemplo, a definição de um recipiente migrado para um recipiente dividido será a seguinte:

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

Após a migração do contentor, pode criar documentos povoando a `_partitionKey` propriedade juntamente com as outras propriedades do documento. A `_partitionKey` propriedade representa a chave de partição dos seus documentos.

A escolha da chave de partição certa é importante para utilizar a produção a provisionada da melhor forma. Para mais informações, consulte como escolher um artigo [chave de partição.](partitioning-overview.md)

> [!NOTE]
> Só pode tirar partido da chave de partição definida pelo sistema se estiver a utilizar a versão mais recente/V3 de SDKs em todos os idiomas.

O exemplo a seguir mostra um código de amostra para criar um documento com a chave de partição definida do sistema e ler esse documento:

**Representação do documento**

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

Para obter a amostra completa, consulte o repositório [.Net][1] GitHub.
                      
## <a name="migrate-the-documents"></a>Migrar os documentos

Enquanto a definição do recipiente é melhorada com uma propriedade chave de partição, os documentos dentro do recipiente não são migrados automaticamente. O que significa que o caminho da propriedade chave da partição do sistema `/_partitionKey` não é automaticamente adicionado aos documentos existentes. É necessário repartir os documentos existentes lendo os documentos que foram criados sem uma chave de partição e reescrevê-los de volta com `_partitionKey` propriedade nos documentos.

## <a name="access-documents-that-dont-have-a-partition-key"></a>Aceder a documentos que não têm uma chave de partição

As aplicações podem aceder aos documentos existentes que não têm uma chave de partição utilizando a propriedade especial do sistema chamada "PartitionKey.None", este é o valor dos documentos não migrados. Você pode usar esta propriedade em todas as operações CRUD e consultas. O exemplo a seguir mostra uma amostra para ler um único documento do NonePartitionKey. 

```csharp
CosmosItemResponse<DeviceInformationItem> readResponse = 
await migratedContainer.Items.ReadItemAsync<DeviceInformationItem>( 
  partitionKey: PartitionKey.None, 
  id: device.Id
); 

```

Para obter a amostra completa sobre como repartir os documentos, consulte as [amostras .Net][1] GitHub. 

## <a name="compatibility-with-sdks"></a>Compatibilidade com SDKs

Versão mais antiga de Azure Cosmos DB SDKs como V2.x.x e V1.x.x não suportam a propriedade chave de partição definida pelo sistema. Então, quando se lê a definição do recipiente de um SDK mais antigo, não contém nenhuma definição de chave de partição e estes recipientes comportar-se-ão exatamente como antes. As aplicações que são construídas com a versão mais antiga dos SDKs continuam a funcionar com não-partitioned como é sem alterações. 

Se um recipiente migrado for consumido pela versão mais recente/V3 do SDK e começar a povoar a chave de partição definida pelo sistema dentro dos novos documentos, já não pode aceder (ler, atualizar, excluir, consultar) esses documentos dos SDKs mais antigos.

## <a name="known-issues"></a>Problemas conhecidos

**A consulta para a contagem de itens que foram inseridos sem uma chave de partição utilizando V3 SDK pode envolver um maior consumo de produção**

Se consultar do V3 SDK os itens que são inseridos utilizando V2 SDK, ou os itens inseridos utilizando o V3 SDK com `PartitionKey.None` parâmetro, a consulta de contagem pode consumir mais RU/s se o `PartitionKey.None` parâmetro for fornecido nas Opções de Alimentação. Recomendamos que não forneça o `PartitionKey.None` parâmetro se não forem inseridos outros itens com uma chave de partição.

Se forem inseridos novos itens com valores diferentes para a chave de partição, a consulta de tal item conta ao passar a chave apropriada `FeedOptions` não terá quaisquer problemas. Depois de inserir novos documentos com chave de partição, se precisar de consultar apenas a contagem do documento sem o valor da chave de partição, essa consulta poderá voltar a incorrer em RU/s mais elevado semelhante às coleções divisórias regulares.

## <a name="next-steps"></a>Passos seguintes

* [Criação de partições no Azure Cosmos DB](partitioning-overview.md)
* [Unidades de Pedido no Azure Cosmos DB](request-units.md)
* [Aprovisionar o débito em contentores e bases de dados](set-throughput.md)
* [Trabalhar com uma conta do Azure Cosmos](./account-databases-containers-items.md)

[1]: https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/NonPartitionContainerMigration