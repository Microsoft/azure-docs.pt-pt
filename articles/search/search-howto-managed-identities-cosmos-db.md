---
title: Criar uma ligação a uma conta Desmesubo Cosmos usando uma identidade gerida
titleSuffix: Azure Cognitive Search
description: Saiba como configurar uma ligação indexante a uma conta Descosso cosmos usando uma identidade gerida
manager: luisca
author: markheff
ms.author: maheff
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: 2a1744feedc3e0ffae6cf2cd45cd090a6c2f06d5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "93422098"
---
# <a name="set-up-an-indexer-connection-to-a-cosmos-db-database-using-a-managed-identity"></a>Configurar uma ligação indexante a uma base de dados de Cossm no Cosmos usando uma identidade gerida

Esta página descreve como configurar uma ligação indexante a uma base de dados DB Azure Cosmos usando uma identidade gerida em vez de fornecer credenciais na cadeia de ligação de objetos de fonte de dados.

Antes de aprender mais sobre esta funcionalidade, recomenda-se que tenha uma compreensão do que é um indexante e como configurar um indexante para a sua fonte de dados. Mais informações podem ser encontradas nos seguintes links:

* [Descrição geral do Indexador](search-indexer-overview.md)
* [Indexador da BD do Cosmos para o Azure](search-howto-index-cosmosdb.md)

## <a name="set-up-a-connection-using-a-managed-identity"></a>Criar uma ligação usando uma identidade gerida

### <a name="1---turn-on-system-assigned-managed-identity"></a>1 - Ligue a identidade gerida atribuída pelo sistema

Quando uma identidade gerida atribuída ao sistema está ativada, a Azure cria uma identidade para o seu serviço de pesquisa que pode ser usado para autenticar outros serviços Azure dentro do mesmo inquilino e subscrição. Em seguida, pode utilizar esta identidade em atribuições de controlo de acesso baseado em funções Azure (Azure RBAC) que permitem o acesso aos dados durante a indexação.

![Ligue o sistema atribuído identidade gerida](./media/search-managed-identities/turn-on-system-assigned-identity.png "Ligue o sistema atribuído identidade gerida")

Depois de **selecionar, verá** um ID de objeto que foi atribuído ao seu serviço de pesquisa.

![ID do Objeto](./media/search-managed-identities/system-assigned-identity-object-id.png "ID do Objeto")
 
### <a name="2---add-a-role-assignment"></a>2 - Adicionar uma atribuição de função

Neste passo, você dará ao seu serviço de Pesquisa Cognitiva Azure permissão para ler dados da sua base de dados Cosmos DB.

1. No portal Azure, navegue para a conta Cosmos DB que contém os dados que gostaria de indexar.
2. Selecione **Controlo de acesso (IAM)**
3. **Selecione Adicionar** e adicionar a **atribuição de função**

    ![Adicionar atribuição de função](./media/search-managed-identities/add-role-assignment-cosmos-db.png "Adicionar atribuição de função")

4. Selecione a **função de leitor de conta de cosm de Cosmos DB**
5. Deixar **Atribuir acesso a** utilizador, grupo ou principal de serviço **Azure**
6. Procure o seu serviço de pesquisa, selecione-o e, em seguida, **selecione Guardar**

    ![Adicionar atribuição de função de leitor e acesso a dados](./media/search-managed-identities/add-role-assignment-cosmos-db-account-reader-role.png "Adicionar atribuição de função de leitor e acesso a dados")

### <a name="3---create-the-data-source"></a>3 - Criar a fonte de dados

A [API REST,](/rest/api/searchservice/create-data-source)o portal Azure e o [.NET SDK](/dotnet/api/azure.search.documents.indexes.models.searchindexerdatasourcetype) suportam a cadeia de ligação de identidade gerida. Abaixo está um exemplo de como criar uma fonte de dados para indexar dados da Cosmos DB usando a [API REST](/rest/api/searchservice/create-data-source) e uma cadeia de conexão de identidade gerida. O formato de cadeia de ligação de identidade gerida é o mesmo para a API REST, .NET SDK e o portal Azure.

Ao utilizar identidades geridas para autenticar, as **credenciais** não incluirão uma chave de conta.

```
POST https://[service name].search.windows.net/datasources?api-version=2020-06-30
Content-Type: application/json
api-key: [Search service admin key]

{
    "name": "cosmos-db-datasource",
    "type": "cosmosdb",
    "credentials": {
        "connectionString": "Database=sql-test-db;ResourceId=/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/cosmos-db-resource-group/providers/Microsoft.DocumentDB/databaseAccounts/my-cosmos-db-account/;"
    },
    "container": { "name": "myCollection", "query": null },
    "dataChangeDetectionPolicy": {
        "@odata.type": "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
        "highWaterMarkColumnName": "_ts"
    }
}
```    

O corpo do pedido contém a definição de fonte de dados, que deve incluir os seguintes campos:

| Campo   | Descrição |
|---------|-------------|
| **nome** | Obrigatório. Escolha qualquer nome para representar o seu objeto de origem de dados. |
|**tipo**| Obrigatório. Deve `cosmosdb` ser. |
|**credenciais** | Obrigatório. <br/><br/>Ao ligar-se utilizando uma identidade gerida, o formato **de credenciais** deve ser: Base de *dados=[nome da base de dados]; ResourceId=[resource-id-string];(ApiKind=[api-kind];)*<br/> <br/>O formato ResourceId: *ResourceId=/subscrições/**o seu ID** de subscrição /grupos de recursos/**o nome do grupo de recursos**/fornecedores/Microsoft.DocumentDB/databaseSAcounts/ o seu nome de **conta cosmos db**/;*<br/><br/>Para as coleções SQL, a cadeia de ligação não requer uma ApiKind.<br/><br/>Para as coleções MongoDB, adicione **ApiKind=MongoDb** à cadeia de ligação. <br/><br/>Para gráficos gremlin e tabelas Cassandra, inscreva-se para a [pré-visualização](https://aka.ms/azure-cognitive-search/indexer-preview) do indexante fechado para ter acesso à pré-visualização e informações sobre como formatar as credenciais.<br/>|
| **recipiente** | Contém os seguintes elementos: <br/>**nome:** Obrigatório. Especifique o ID da recolha da base de dados a ser indexado.<br/>**consulta**: Opcional. Pode especificar uma consulta para aplainar um documento JSON arbitrário num esquema plano que a Azure Cognitive Search pode indexar.<br/>Para a API mongodb, Gremlin API e Cassandra API, as consultas não são apoiadas. |
| **dataChangeDetectionPolicy** | Recomendado |
|**dataDeletionDetectionPolicy** | Opcional |

### <a name="4---create-the-index"></a>4 - Criar o índice

O índice especifica os campos num documento, atributos e outras construções que moldam a experiência de pesquisa.

Aqui está como criar um índice com um `booktitle` campo pesquisável:

```
POST https://[service name].search.windows.net/indexes?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
    "name" : "my-target-index",
    "fields": [
    { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
    { "name": "booktitle", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
    ]
}
```

Para obter mais informações sobre a criação de índices, consulte [Criar Índice](/rest/api/searchservice/create-index)

### <a name="5---create-the-indexer"></a>5 - Criar o indexador

Um indexante conecta uma fonte de dados com um índice de pesquisa de destino e fornece um calendário para automatizar a atualização de dados.

Uma vez criado o índice e a fonte de dados, está pronto para criar o indexante.

Definição de indexante de exemplo:

```http
    POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "cosmos-db-indexer",
      "dataSourceName" : "cosmos-db-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" }
    }
```

Este indexante será executado de duas em duas horas (o intervalo de horário está definido para "PT2H"). Para executar um indexante a cada 30 minutos, desajuste o intervalo para "PT30M". O intervalo suportado mais curto é de 5 minutos. O horário é opcional - se omitido, um indexante funciona apenas uma vez quando é criado. No entanto, pode executar um indexante a qualquer momento.   

Para obter mais detalhes sobre a API do Indexante Create, consulte [Create Indexer](/rest/api/searchservice/create-indexer).

Para obter mais informações sobre a definição de horários de indexantes, consulte [Como agendar indexadores para a Pesquisa Cognitiva do Azure](search-howto-schedule-indexers.md).

## <a name="troubleshooting"></a>Resolução de problemas

Se descobrir que não é capaz de indexar dados da Cosmos DB considere o seguinte:

1. Se alterou recentemente as teclas de conta Cosmos DB, terá de esperar até 15 minutos para que a cadeia de ligação de identidade gerida funcione.

1. Verifique se a conta Cosmos DB tem o seu acesso restrito a redes selecionadas. Se o fizer, consulte o [Indexer acesso a conteúdos protegidos por funcionalidades de segurança da rede Azure](search-indexer-securing-resources.md).

## <a name="next-steps"></a>Passos seguintes

* [Indexador da BD do Cosmos para o Azure](search-howto-index-cosmosdb.md)