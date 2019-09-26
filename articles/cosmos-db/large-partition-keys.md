---
title: Crie contêineres de Cosmos do Azure com chave de partição grande usando o portal do Azure e vários SDKs.
description: Saiba como criar um contêiner em Azure Cosmos DB com chave de partição grande usando portal do Azure e SDKs diferentes.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/03/2019
ms.author: mjbrown
ms.openlocfilehash: a1216daade2df832b606fceb648fca998c3fdec8
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/26/2019
ms.locfileid: "71300140"
---
# <a name="create-containers-with-large-partition-key"></a>Criar contêineres com chave de partição grande

Azure Cosmos DB usa o esquema de particionamento baseado em hash para atingir o dimensionamento horizontal dos dados. Todos os contêineres de Cosmos do Azure criados antes de maio de 3 2019 usam uma função de hash que computa o hash com base nos primeiros 100 bytes da chave de partição. Se houver várias chaves de partição que tenham os mesmos primeiros 100 bytes, essas partições lógicas serão consideradas como a mesma partição lógica pelo serviço. Isso pode levar a problemas, como a cota de tamanho da partição sendo incorreta e a aplicação de índices exclusivos nas chaves de partição. Chaves de partição grandes são introduzidas para resolver esse problema. O Azure Cosmos DB agora dá suporte a chaves de partição grandes com valores de até 2 KB.

Chaves de partição grandes têm suporte usando a funcionalidade de uma versão aprimorada da função de hash, que pode gerar um hash exclusivo de chaves de partição grandes de até 2 KB. Essa versão de hash também é recomendada para cenários com alta cardinalidade de chave de partição, independentemente do tamanho da chave de partição. Uma cardinalidade de chave de partição é definida como o número de partições lógicas exclusivas, por exemplo, na ordem de ~ 30000 partições lógicas em um contêiner. Este artigo descreve como criar um contêiner com uma chave de partição grande usando o portal do Azure e diferentes SDKs. 

## <a name="create-a-large-partition-key-net-sdk"></a>Criar uma chave de partição grande (SDK do .net)

Para criar um contêiner com uma chave de partição grande usando o SDK do .net, `PartitionKeyDefinitionVersion.V2` especifique a propriedade. O exemplo a seguir mostra como especificar a Propriedade Version dentro do objeto PartitionKeyDefinition e defini-la como PartitionKeyDefinitionVersion. v2.

### <a name="v3-net-sdk"></a>SDK do .NET v3

```csharp
await database.CreateContainerAsync(
    new ContainerProperties(collectionName, $"/longpartitionkey")
    {
        PartitionKeyDefinitionVersion = PartitionKeyDefinitionVersion.V2, 
    })
```

### <a name="v2-net-sdk"></a>v2 .NET SDK

```csharp
DocumentCollection collection = await newClient.CreateDocumentCollectionAsync(
database,
     new DocumentCollection
        {
           Id = Guid.NewGuid().ToString(),
           PartitionKey = new PartitionKeyDefinition
           {
             Paths = new Collection<string> {"/longpartitionkey" },
             Version = PartitionKeyDefinitionVersion.V2
           }
         },
      new RequestOptions { OfferThroughput = 400 });
```

## <a name="create-a-large-partition-key-azure-portal"></a>Criar uma chave de partição grande (portal do Azure) 

Para criar uma chave de partição grande, enquanto você cria um novo contêiner usando o portal do Azure, verifique a opção **minha chave de partição é maior que 100-bytes** . Por padrão, todos os novos contêineres são aceitos usando as chaves de partição grandes. Desmarque a caixa de seleção se você não precisar de chaves de partição grandes ou se tiver aplicativos em execução na versão de SDKs com mais de 1,18.

![Criar chaves de partição grandes usando portal do Azure](./media/large-partition-keys/large-partition-key-with-portal.png)

## <a name="create-a-large-partition-key-powershell"></a>Criar uma chave de partição grande (PowerShell)

Para criar um contêiner com uma chave de partição grande usando o PowerShell `"version" = 2` , inclua `partitionKey` para o objeto.

```azurepowershell-interactive
# Create a Cosmos SQL API container with large partition key support (version 2)
$resourceGroupName = "myResourceGroup"
$containerName = "mycosmosaccount" + "/sql/" + "myDatabase" + "/" + "myContainer"

# Container with large partition key support (version = 2)
$containerProperties = @{
  "resource"=@{
    "id"=$containerName;
    "partitionKey"=@{
        "paths"=@("/myPartitionKey");
        "kind"="Hash";
        "version" = 2
    };
    "indexingPolicy"=@{
        "indexingMode"="Consistent";
        "includedPaths"= @(@{
            "path"="/*"
        });
        "excludedPaths"= @(@{
            "path"="/myPathToNotIndex/*"
        })
    }
  }
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $containerName -PropertyObject $containerProperties
```

## <a name="supported-sdk-versions"></a>Versões do SDK com suporte

As chaves de partição grandes têm suporte com as seguintes versões mínimas dos SDKs:

|Tipo de SDK  | Versão mínima   |
|---------|---------|
|.Net     |    1,18     |
|Sincronização de Java     |   2.4.0      |
|Assíncrono de Java   |  2.5.0        |
| API REST | versão superior `2017-05-03` a usando o cabeçalho `x-ms-version` de solicitação.|

No momento, você não pode usar contêineres com chave de partição grande dentro do Power BI e aplicativos lógicos do Azure. Você pode usar contêineres sem uma chave de partição grande desses aplicativos.

## <a name="next-steps"></a>Passos seguintes

* [Criação de partições no Azure Cosmos DB](partitioning-overview.md)
* [Unidades de Pedido no Azure Cosmos DB](request-units.md)
* [Provisionar taxa de transferência em contêineres e bancos de dados](set-throughput.md)
* [Trabalhar com a conta do Azure Cosmos](account-overview.md)


