---
title: Migrar contêineres de Cosmos do Azure não particionados para contêineres particionados
description: Saiba como migrar todos os contêineres não particionados existentes para contêineres particionados.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/25/2019
ms.author: mjbrown
ms.openlocfilehash: 3a13f8928ba243195c30200dae0525e72c1c161b
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/03/2019
ms.locfileid: "71844409"
---
# <a name="migrate-non-partitioned-containers-to-partitioned-containers"></a>Migrar contêineres não particionados para contêineres particionados

Azure Cosmos DB dá suporte à criação de contêineres sem uma chave de partição. No momento, você pode criar contêineres não particionados usando CLI do Azure e Azure Cosmos DB SDKs (.net, Java, NodeJs) que têm uma versão menor ou igual a 2. x. Você não pode criar contêineres não particionados usando o portal do Azure. No entanto, esses contêineres não particionados não são elásticos e têm capacidade de armazenamento fixa de 10 GB e limite de taxa de transferência de 10K RU/s.

Os contêineres não particionados são herdados e você deve migrar seus contêineres não particionados existentes para contêineres particionados para dimensionar o armazenamento e a taxa de transferência. Azure Cosmos DB fornece um mecanismo definido pelo sistema para migrar seus contêineres não particionados para contêineres particionados. Este documento explica como todos os contêineres não particionados existentes são migrados automaticamente para os contêineres particionados. Você poderá aproveitar o recurso de migração automática somente se estiver usando a versão v3 de SDKs em todos os idiomas.

> [!NOTE]
> No momento, não é possível migrar Azure Cosmos DB contas de API do MongoDB e do Gremlin usando as etapas descritas neste documento.

## <a name="migrate-container-using-the-system-defined-partition-key"></a>Migrar o contêiner usando a chave de partição definida pelo sistema

Para dar suporte à migração, o Azure Cosmos DB fornece uma chave de partição `/_partitionkey` definida pelo sistema chamada em todos os contêineres que não têm uma chave de partição. Você não pode alterar a definição de chave de partição depois que os contêineres são migrados. Por exemplo, a definição de um contêiner que é migrado para um contêiner particionado será a seguinte:

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

Depois que o contêiner é migrado, você pode criar documentos preenchendo a `_partitionKey` Propriedade junto com as outras propriedades do documento. A `_partitionKey` propriedade representa a chave de partição de seus documentos.

A escolha da chave de partição correta é importante para utilizar a taxa de transferência provisionada de forma ideal. Para obter mais informações, consulte [o artigo como escolher uma chave de partição](partitioning-overview.md) .

> [!NOTE]
> Você só poderá aproveitar a chave de partição definida pelo sistema se estiver usando a versão mais recente/v3 dos SDKs em todos os idiomas.

O exemplo a seguir mostra um código de exemplo para criar um documento com a chave de partição definida pelo sistema e ler esse documento:

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

Para obter o exemplo completo, consulte o repositório GitHub de [exemplos do .net](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/CodeSamples) .
                      
## <a name="migrate-the-documents"></a>Migrar os documentos

Embora a definição de contêiner seja aprimorada com uma propriedade de chave de partição, os documentos dentro do contêiner não são migrados automaticamente. Isso significa que o caminho da propriedade `/_partitionKey` de chave de partição do sistema não é adicionado automaticamente aos documentos existentes. Você precisa reparticionar os documentos existentes lendo os documentos que foram criados sem uma chave de partição e regravá-los novamente `_partitionKey` com a propriedade nos documentos.

## <a name="access-documents-that-dont-have-a-partition-key"></a>Acessar documentos que não têm uma chave de partição

Os aplicativos podem acessar os documentos existentes que não têm uma chave de partição usando a propriedade especial do sistema chamada "PartitionKey. None", esse é o valor dos documentos não migrados. Você pode usar essa propriedade em todas as operações CRUD e de consulta. O exemplo a seguir mostra um exemplo para ler um único documento do NonePartitionKey. 

```csharp
CosmosItemResponse<DeviceInformationItem> readResponse = 
await migratedContainer.Items.ReadItemAsync<DeviceInformationItem>( 
  partitionKey: PartitionKey.None, 
  id: device.Id
); 

```

Para obter o exemplo completo sobre como reparticionar os documentos, consulte o repositório GitHub de [exemplos do .net](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/CodeSamples) . 

## <a name="compatibility-with-sdks"></a>Compatibilidade com SDKs

A versão mais antiga dos SDKs de Azure Cosmos DB como v2. x. x e v1. x. x não oferece suporte à propriedade de chave de partição definida pelo sistema. Portanto, quando você lê a definição de contêiner de um SDK mais antigo, ele não contém nenhuma definição de chave de partição e esses contêineres se comportarão exatamente como antes. Os aplicativos que são criados com a versão mais antiga dos SDKs continuam a funcionar com não particionados, sem nenhuma alteração. 

Se um contêiner migrado for consumido pela versão mais recente/V3 do SDK e você começar a popular a chave de partição definida pelo sistema nos novos documentos, você não poderá mais acessar (ler, atualizar, excluir, consultar) esses documentos a partir dos SDKs mais antigos.

## <a name="next-steps"></a>Passos seguintes

* [Criação de partições no Azure Cosmos DB](partitioning-overview.md)
* [Unidades de Pedido no Azure Cosmos DB](request-units.md)
* [Provisionar taxa de transferência em contêineres e bancos de dados](set-throughput.md)
* [Trabalhar com a conta do Azure Cosmos](account-overview.md)