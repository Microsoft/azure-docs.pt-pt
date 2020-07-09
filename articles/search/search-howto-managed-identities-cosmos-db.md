---
title: Criar uma ligação a uma conta Desmesubo Cosmos utilizando uma identidade gerida (pré-visualização)
titleSuffix: Azure Cognitive Search
description: Saiba como configurar uma ligação indexante a uma conta Desmesubo cosmos utilizando uma identidade gerida (pré-visualização)
manager: luisca
author: markheff
ms.author: maheff
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 05/18/2020
ms.openlocfilehash: c67a5537a74e37473280fbd44fa47c65f2a37806
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85563148"
---
# <a name="set-up-an-indexer-connection-to-a-cosmos-db-database-using-a-managed-identity-preview"></a>Configurar uma ligação indexante a uma base de dados cosmos DB utilizando uma identidade gerida (pré-visualização)

> [!IMPORTANT] 
> O suporte para a criação de uma ligação a uma fonte de dados utilizando uma identidade gerida encontra-se atualmente numa pré-visualização pública fechada. A funcionalidade de pré-visualização é fornecida sem um contrato de nível de serviço, e não é recomendada para cargas de trabalho de produção.
> Pode solicitar acesso à pré-visualização preenchendo [este formulário](https://aka.ms/azure-cognitive-search/mi-preview-request).

Esta página descreve como configurar uma ligação indexante a uma base de dados DB Azure Cosmos usando uma identidade gerida em vez de fornecer credenciais na cadeia de ligação de objetos de fonte de dados.

Antes de aprender mais sobre esta funcionalidade, recomenda-se que tenha uma compreensão do que é um indexante e como configurar um indexante para a sua fonte de dados. Mais informações podem ser encontradas nos seguintes links:
* [Descrição geral do Indexador](search-indexer-overview.md)
* [Indexador da BD do Cosmos para o Azure](search-howto-index-cosmosdb.md)

## <a name="set-up-a-connection-using-a-managed-identity"></a>Criar uma ligação usando uma identidade gerida

### <a name="1---turn-on-system-assigned-managed-identity"></a>1 - Ligue a identidade gerida atribuída pelo sistema

Quando uma identidade gerida atribuída ao sistema está ativada, a Azure cria uma identidade para o seu serviço de pesquisa que pode ser usado para autenticar outros serviços Azure dentro do mesmo inquilino e subscrição. Em seguida, pode utilizar esta identidade em atribuições de controlo de acesso baseado em funções (RBAC) que permitem o acesso aos dados durante a indexação.

![Ligue o sistema atribuído identidade gerida](./media/search-managed-identities/turn-on-system-assigned-identity.png "Ligue o sistema atribuído identidade gerida")

Depois de **selecionar, verá** um ID de objeto que foi atribuído ao seu serviço de pesquisa.

![ID de objeto](./media/search-managed-identities/system-assigned-identity-object-id.png "ID de objeto")
 
### <a name="2---add-a-role-assignment"></a>2 - Adicionar uma atribuição de função

Neste passo, você dará ao seu serviço de Pesquisa Cognitiva Azure permissão para ler dados da sua base de dados Cosmos DB.

1. No portal Azure, navegue para a conta Cosmos DB que contém os dados que gostaria de indexar.
2. Selecione **controlo de acesso (IAM)**
3. **Selecione Adicionar** e adicionar a **atribuição de função**

    ![Adicionar atribuição de função](./media/search-managed-identities/add-role-assignment-cosmos-db.png "Adicionar atribuição de função")

4. Selecione a **função de leitor de conta de cosm de Cosmos DB**
5. Deixar **Atribuir acesso a** utilizador, grupo ou principal de serviço **Azure**
6. Procure o seu serviço de pesquisa, selecione-o e, em seguida, **selecione Guardar**

    ![Adicionar atribuição de função de leitor e acesso a dados](./media/search-managed-identities/add-role-assignment-cosmos-db-account-reader-role.png "Adicionar atribuição de função de leitor e acesso a dados")

### <a name="3---create-the-data-source"></a>3 - Criar a fonte de dados

Uma **fonte de dados** especifica os dados para indexar, credenciais e políticas para identificar alterações nos dados (tais como documentos modificados ou eliminados dentro da sua recolha). A fonte de dados é definida como um recurso independente para que possa ser usada por vários indexadores.

Ao utilizar identidades geridas para autenticar a fonte de dados, as **credenciais** não incluirão uma chave de conta.

Exemplo de como criar um objeto de origem de dados Do Cosmos DB utilizando a [API REST](https://docs.microsoft.com/rest/api/searchservice/create-data-source):

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
| **nome** | Necessário. Escolha qualquer nome para representar o seu objeto de origem de dados. |
|**tipo**| Necessário. Deve `cosmosdb` ser. |
|**credenciais** | Necessário. <br/><br/>Ao ligar-se utilizando uma identidade gerida, o formato **de credenciais** deve ser: Base de *dados=[nome da base de dados]; ResourceId=[resource-id-string];(ApiKind=[api-kind];)*<br/> <br/>O formato ResourceId: *ResourceId=/subscrições/**o seu ID**de subscrição /grupos de recursos/**o nome do grupo de recursos**/fornecedores/Microsoft.DocumentDB/databaseSAcounts/ o seu nome de**conta cosmos db**/;*<br/><br/>Para as coleções SQL, a cadeia de ligação não requer uma ApiKind.<br/><br/>Para as coleções MongoDB, adicione **ApiKind=MongoDb** à cadeia de ligação. <br/><br/>Para gráficos gremlin e tabelas Cassandra, inscreva-se para a [pré-visualização](https://aka.ms/azure-cognitive-search/indexer-preview) do indexante fechado para ter acesso à pré-visualização e informações sobre como formatar as credenciais.<br/>|
| **recipiente** | Contém os seguintes elementos: <br/>**nome:** Obrigatório. Especifique o ID da recolha da base de dados a ser indexado.<br/>**consulta**: Opcional. Pode especificar uma consulta para aplainar um documento JSON arbitrário num esquema plano que a Azure Cognitive Search pode indexar.<br/>Para a API mongodb, Gremlin API e Cassandra API, as consultas não são apoiadas. |
| **dataChangeDetectionPolicy** | Recomendado |
|**dataDeletionDetectionPolicy** | Opcional |

O portal Azure e o [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasource?view=azure-dotnet) também suportam a cadeia de ligação de identidades geridas. O portal Azure requer uma bandeira de recurso que lhe será fornecida quando se inscrever para a pré-visualização usando o link no topo desta página. 

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

Para obter mais informações sobre a criação de índices, consulte [Criar Índice](https://docs.microsoft.com/rest/api/searchservice/create-index)

### <a name="5---create-the-indexer"></a>5 - Criar o indexador

Um indexante conecta uma fonte de dados com um índice de pesquisa de destino e fornece um calendário para automatizar a atualização de dados.

Uma vez criado o índice e a fonte de dados, está pronto para criar o indexante.

Definição de indexante de exemplo:

    POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "cosmos-db-indexer",
      "dataSourceName" : "cosmos-db-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" }
    }

Este indexante será executado de duas em duas horas (o intervalo de horário está definido para "PT2H"). Para executar um indexante a cada 30 minutos, desajuste o intervalo para "PT30M". O intervalo suportado mais curto é de 5 minutos. O horário é opcional - se omitido, um indexante funciona apenas uma vez quando é criado. No entanto, pode executar um indexante a qualquer momento.   

Para obter mais detalhes sobre a API do Indexante Create, consulte [Create Indexer](https://docs.microsoft.com/rest/api/searchservice/create-indexer).

Para obter mais informações sobre a definição de horários de indexantes, consulte [Como agendar indexadores para a Pesquisa Cognitiva do Azure](search-howto-schedule-indexers.md).

## <a name="see-also"></a>Veja também

Saiba mais sobre os indexantes da Cosmos DB:
* [Indexador da BD do Cosmos para o Azure](search-howto-index-cosmosdb.md)
