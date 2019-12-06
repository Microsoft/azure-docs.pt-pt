---
title: 'Tutorial: Azure Cosmos DB tutorial de distribuição global para a API do SQL'
description: 'Tutorial: saiba como configurar a distribuição global Azure Cosmos DB usando a API do SQL com .net, Java, Python e vários outros SDKs'
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: tutorial
ms.date: 11/05/2019
ms.reviewer: sngun
ms.openlocfilehash: 541fcdd966ec1e0443fa6211d894bab3ed965f93
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74870331"
---
# <a name="tutorial-set-up-azure-cosmos-db-global-distribution-using-the-sql-api"></a>Tutorial: configurar a distribuição global Azure Cosmos DB usando a API do SQL

Neste artigo, vamos mostrar como utilizar o portal do Azure para configurar a distribuição global do Azure Cosmos DB e, em seguida, ligar através da API SQL.

Este artigo abrange as seguintes tarefas: 

> [!div class="checklist"]
> * Configurar a distribuição global com o portal do Azure
> * Configurar a distribuição global com as [APIs SQL](sql-api-introduction.md)

<a id="portal"></a>
[!INCLUDE [cosmos-db-tutorial-global-distribution-portal](../../includes/cosmos-db-tutorial-global-distribution-portal.md)]


## <a name="connecting-to-a-preferred-region-using-the-sql-api"></a>Ligar a uma região preferencial com a API SQL

Para tirar o máximo partido da [distribuição global](distribute-data-globally.md), as aplicações cliente podem especificar a lista de preferência ordenada de regiões a utilizar nas operações de documentos. Isto pode ser feito ao definir a política de ligação. Com base na configuração da conta do Azure Cosmos DB, disponibilidade regional atual e lista de preferência especificada, o SDK SQL selecionará o ponto final ideal para efetuar operações de escrita e leitura.

Esta lista de preferência é especificada ao inicializar uma ligação com os SDKs SQL. Os SDKs aceitam um parâmetro opcional "PreferredLocations" que é uma lista ordenada de regiões do Azure.

O SDK enviará automaticamente todas as escritas para a região de escrita atual.

Todas as leituras serão enviadas para a primeira região disponível na lista PreferredLocations. Se o pedido falhar, o cliente falhará a lista para a região seguinte e assim sucessivamente.

Os SDKs só tentarão ler a partir das regiões especificadas em PreferredLocations. Por exemplo, se a Conta de Base de Dados estiver disponível em quatro regiões, mas o cliente apenas especificar duas regiões de leitura (não escrita) para PreferredLocations, não serão fornecidas leituras fora da região de leitura não especificada em PreferredLocations. Se as regiões de leitura especificadas em PreferredLocations não estiverem disponíveis, serão fornecidas leituras fora da região de escrita.

A aplicação pode verificar o ponto final de escrita atual e o ponto final de leitura escolhido pelo SDK ao consultar duas propriedades, WriteEndpoint e ReadEndpoint, disponíveis na versão 1.8 e posterior do SDK.

Se a propriedade PreferredLocations não estiver definida, todos os pedidos serão fornecidos a partir da região de escrita atual.

## <a name="net-sdk"></a>.NET SDK
O SDK pode ser utilizado sem quaisquer alterações de código. Neste caso, o SDK direciona automaticamente as leituras e as escritas para a região de escrita atual.

Na versão 1.8 e posterior do SDK .NET, o parâmetro ConnectionPolicy do construtor DocumentClient tem uma propriedade denominada Microsoft.Azure.Documents.ConnectionPolicy.PreferredLocations. Esta propriedade é do tipo de Coleção `<string>` e deve conter uma lista de nomes de região. Os valores de cadeia de caracteres são formatados de acordo com a coluna nome da região na página [regiões do Azure][regions] , sem espaços antes ou depois do primeiro e do último caractere, respectivamente.

Os pontos finais de escrita e leitura atuais estão disponíveis em DocumentClient.WriteEndpoint e DocumentClient.ReadEndpoint, respetivamente.

> [!NOTE]
> Os URLs para os pontos finais não devem ser considerados como constantes de longa duração. O serviço pode atualizá-los em qualquer momento. O SDK processa esta alteração automaticamente.
>
>

```csharp
// Getting endpoints from application settings or other configuration location
Uri accountEndPoint = new Uri(Properties.Settings.Default.GlobalDatabaseUri);
string accountKey = Properties.Settings.Default.GlobalDatabaseKey;
  
ConnectionPolicy connectionPolicy = new ConnectionPolicy();

//Setting read region selection preference
connectionPolicy.PreferredLocations.Add(LocationNames.WestUS); // first preference
connectionPolicy.PreferredLocations.Add(LocationNames.EastUS); // second preference
connectionPolicy.PreferredLocations.Add(LocationNames.NorthEurope); // third preference

// initialize connection
DocumentClient docClient = new DocumentClient(
    accountEndPoint,
    accountKey,
    connectionPolicy);

// connect to DocDB
await docClient.OpenAsync().ConfigureAwait(false);
```

## <a name="nodejsjavascript"></a>Node. js/JavaScript

> [!NOTE]
> Os URLs para os pontos finais não devem ser considerados como constantes de longa duração. O serviço pode atualizá-los em qualquer momento. O SDK processará esta alteração automaticamente.
>
>

Veja abaixo um exemplo de código para node. js/JavaScript.

```JavaScript
// Setting read region selection preference, in the following order -
// 1 - West US
// 2 - East US
// 3 - North Europe
const preferredLocations = ['West US', 'East US', 'North Europe'];

// initialize the connection
const client = new CosmosClient{ endpoint, key, connectionPolicy: { preferredLocations } });
```

## <a name="python-sdk"></a>Python SDK

O código a seguir mostra como definir locais preferenciais usando o SDK do Python:

```python

connectionPolicy = documents.ConnectionPolicy()
connectionPolicy.PreferredLocations = ['West US', 'East US', 'North Europe']
client = cosmos_client.CosmosClient(ENDPOINT, {'masterKey': MASTER_KEY}, connectionPolicy)

```

## <a name="java-v2-sdk"></a>SDK do Java v2

O código a seguir mostra como definir locais preferenciais usando o SDK do Java:

```java
ConnectionPolicy policy = new ConnectionPolicy();
policy.setUsingMultipleWriteLocations(true);
policy.setPreferredLocations(Arrays.asList("East US", "West US", "Canada Central"));
AsyncDocumentClient client =
        new AsyncDocumentClient.Builder()
                .withMasterKeyOrResourceToken(this.accountKey)
                .withServiceEndpoint(this.accountEndpoint)
                .withConnectionPolicy(policy)
                .build();
```

## <a name="rest"></a>REST
Depois de uma conta de base de dados ter sido disponibilizada em várias regiões, os clientes podem consultar a respetiva disponibilidade ao efetuar um pedido GET no URI seguinte.

    https://{databaseaccount}.documents.azure.com/

O serviço devolverá uma lista de regiões e os URIs de ponto final correspondentes do Azure Cosmos DB para as réplicas. A região de escrita atual será indicada na resposta. Em seguida, o cliente pode selecionar o ponto final adequado para obter todos os pedidos da API REST da seguinte forma.

Resposta de exemplo

    {
        "_dbs": "//dbs/",
        "media": "//media/",
        "writableLocations": [
            {
                "Name": "West US",
                "DatabaseAccountEndpoint": "https://globaldbexample-westus.documents.azure.com:443/"
            }
        ],
        "readableLocations": [
            {
                "Name": "East US",
                "DatabaseAccountEndpoint": "https://globaldbexample-eastus.documents.azure.com:443/"
            }
        ],
        "MaxMediaStorageUsageInMB": 2048,
        "MediaStorageUsageInMB": 0,
        "ConsistencyPolicy": {
            "defaultConsistencyLevel": "Session",
            "maxStalenessPrefix": 100,
            "maxIntervalInSeconds": 5
        },
        "addresses": "//addresses/",
        "id": "globaldbexample",
        "_rid": "globaldbexample.documents.azure.com",
        "_self": "",
        "_ts": 0,
        "_etag": null
    }


* Todos os pedidos PUT, POST e DELETE têm de ir para o URI de escrita indicado
* Todos os GETs e outros pedidos só de leitura (por exemplo, consultas) podem ir para qualquer ponto final à escolha do cliente

Os pedidos de escrita para regiões só de leitura falharão com o código de erro HTTP 403 ("Proibido").

Se a região de escrita for alterada após a fase de deteção inicial do cliente, as escritas subsequentes para a região de escrita anterior falharão com o código de erro HTTP 403 ("Proibido"). Em seguida, o cliente deve obter novamente a lista de regiões para obter a região de escrita atualizada.

Já está, isto conclui este tutorial. Pode saber como gerir a consistência da sua conta replicada globalmente ao ler [Níveis de consistência no Azure Cosmos DB](consistency-levels.md). Para obter mais informações sobre como funciona a replicação de base de dados global no Azure Cosmos DB, veja [Distribuir dados globalmente com o Azure Cosmos DB](distribute-data-globally.md).

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, fez o seguinte:

> [!div class="checklist"]
> * Configurar a distribuição global com o portal do Azure
> * Configurar a distribuição global com as APIs SQL

Agora, pode avançar para o próximo tutorial para saber como desenvolver localmente com o emulador local do Azure Cosmos DB.

> [!div class="nextstepaction"]
> [Desenvolver localmente com o emulador](local-emulator.md)

[regions]: https://azure.microsoft.com/regions/

