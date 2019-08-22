---
title: Conectar e indexar o conteúdo do banco de dados SQL do Azure usando indexadores-Azure Search
description: Saiba como rastrear dados no banco de dados SQL do Azure usando indexadores para pesquisa de texto completo no Azure Search. Este artigo aborda conexões, configuração do indexador e ingestão de dados.
ms.date: 05/02/2019
author: mgottein
manager: nitinme
ms.author: magottei
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seodec2018
ms.openlocfilehash: 4ed218fdc1c6580e9b92364d123b081a1f34b441
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/20/2019
ms.locfileid: "69656240"
---
# <a name="connect-to-and-index-azure-sql-database-content-using-azure-search-indexers"></a>Conectar e indexar conteúdo do banco de dados SQL do Azure usando indexadores Azure Search

Antes de consultar um [índice de Azure Search](search-what-is-an-index.md), você deve preenchê-lo com seus dados. Se os dados residirem em um Azure SQL Database, um **indexador Azure Search para o banco de dados SQL do Azure** (ou o **indexador SQL do Azure** para curto) pode automatizar o processo de indexação, o que significa menos código para escrever e menos infraestrutura para se preocupar.

Este artigo aborda a mecânica de uso [](search-indexer-overview.md)de indexadores, mas também descreve os recursos disponíveis somente com bancos de dados SQL do Azure (por exemplo, controle de alterações integrado). 

Além dos bancos de dados SQL do Azure, Azure Search fornece indexadores para [Azure Cosmos DB](search-howto-index-cosmosdb.md), [armazenamento de BLOBs do Azure](search-howto-indexing-azure-blob-storage.md)e [armazenamento de tabelas do Azure](search-howto-indexing-azure-tables.md). Para solicitar suporte para outras fontes de dados, forneça seus comentários sobre o [Fórum de comentários Azure Search](https://feedback.azure.com/forums/263029-azure-search/).

## <a name="indexers-and-data-sources"></a>Indexadores e fontes de dados

Uma **fonte de dados** especifica os dados a serem indexados, as credenciais de acesso a dados e as políticas que identificam com eficiência as alterações nos dados (linhas novas, modificadas ou excluídas). Ele é definido como um recurso independente para que possa ser usado por vários indexadores.

Um **indexador** é um recurso que conecta uma única fonte de dados a um índice de pesquisa de destino. Um indexador é usado das seguintes maneiras:

* Execute uma cópia única dos dados para popular um índice.
* Atualize um índice com alterações na fonte de dados em um agendamento.
* Execute sob demanda para atualizar um índice, conforme necessário.

Um único indexador só pode consumir uma tabela ou exibição, mas você pode criar vários indexadores se desejar preencher vários índices de pesquisa. Para obter mais informações sobre conceitos, [consulte operações do indexador: Fluxo de](https://docs.microsoft.com/rest/api/searchservice/Indexer-operations#typical-workflow)trabalho típico.

Você pode configurar e configurar um indexador do SQL Azure usando:

* Assistente de importação de dados no [portal do Azure](https://portal.azure.com)
* [SDK do .net](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer?view=azure-dotnet) Azure Search
* [API REST](https://docs.microsoft.com/rest/api/searchservice/indexer-operations) do Azure Search

Neste artigo, usaremos a API REST para criar indexadores e **fontes de dados**.

## <a name="when-to-use-azure-sql-indexer"></a>Quando usar o indexador do SQL Azure
Dependendo de vários fatores relacionados aos seus dados, o uso do indexador do SQL Azure pode ou não ser apropriado. Se os seus dados atenderem aos seguintes requisitos, você poderá usar o indexador do SQL Azure.

| Critérios | Detalhes |
|----------|---------|
| Os dados são provenientes de uma única tabela ou exibição | Se os dados estiverem dispersos em várias tabelas, você poderá criar uma exibição única dos dados. No entanto, se você usar uma exibição, não poderá usar SQL Server detecção de alteração integrada para atualizar um índice com alterações incrementais. Para obter mais informações, consulte [capturando linhas alteradas e excluídas](#CaptureChangedRows) abaixo. |
| Os tipos de dados são compatíveis | Não há suporte para a maioria dos tipos SQL em um índice Azure Search. Para obter uma lista, consulte [mapeando tipos de dados](#TypeMapping). |
| A sincronização de dados em tempo real não é necessária | Um indexador pode reindexar sua tabela no máximo a cada cinco minutos. Se os dados forem alterados com frequência e as alterações precisarem ser refletidas no índice em segundos ou em minutos, é recomendável usar a [API REST](https://docs.microsoft.com/rest/api/searchservice/AddUpdate-or-Delete-Documents) ou o [SDK do .net](search-import-data-dotnet.md) para enviar linhas atualizadas diretamente. |
| A indexação incremental é possível | Se você tiver um conjunto de dados grande e planejar a execução do indexador em um agendamento, Azure Search deverá ser capaz de identificar com eficiência as linhas novas, alteradas ou excluídas. A indexação não incremental só será permitida se você estiver indexando sob demanda (não na agenda) ou indexando menos de 100.000 linhas. Para obter mais informações, consulte [capturando linhas alteradas e excluídas](#CaptureChangedRows) abaixo. |

> [!NOTE] 
> Azure Search dá suporte apenas à autenticação SQL Server. Se você precisar de suporte para Azure Active Directory autenticação de senha, vote nesta [sugestão](https://feedback.azure.com/forums/263029-azure-search/suggestions/33595465-support-azure-active-directory-password-authentica)do UserVoice.

## <a name="create-an-azure-sql-indexer"></a>Criar um indexador do SQL Azure

1. Criar a fonte de dados:

   ```
    POST https://myservice.search.windows.net/datasources?api-version=2019-05-06
    Content-Type: application/json
    api-key: admin-key

    {
        "name" : "myazuresqldatasource",
        "type" : "azuresql",
        "credentials" : { "connectionString" : "Server=tcp:<your server>.database.windows.net,1433;Database=<your database>;User ID=<your user name>;Password=<your password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30;" },
        "container" : { "name" : "name of the table or view that you want to index" }
    }
   ```

   Você pode obter a cadeia de conexão do [portal do Azure](https://portal.azure.com); Use a `ADO.NET connection string` opção.

2. Crie o índice de Azure Search de destino se você ainda não tiver um. Você pode criar um índice usando o [portal](https://portal.azure.com) ou a [API CREATE INDEX](https://docs.microsoft.com/rest/api/searchservice/Create-Index). Verifique se o esquema do índice de destino é compatível com o esquema da tabela de origem-consulte [mapeamento entre os tipos de dados SQL e Azure Search](#TypeMapping).

3. Crie o indexador dando a ele um nome e fazendo referência à fonte de dados e ao índice de destino:

    ```
    POST https://myservice.search.windows.net/indexers?api-version=2019-05-06
    Content-Type: application/json
    api-key: admin-key

    {
        "name" : "myindexer",
        "dataSourceName" : "myazuresqldatasource",
        "targetIndexName" : "target index name"
    }
    ```

Um indexador criado dessa maneira não tem uma agenda. Ele é executado automaticamente uma vez quando é criado. Você pode executá-lo novamente a qualquer momento usando uma solicitação de **execução** do indexador:

    POST https://myservice.search.windows.net/indexers/myindexer/run?api-version=2019-05-06
    api-key: admin-key

Você pode personalizar vários aspectos do comportamento do indexador, como o tamanho do lote e quantos documentos podem ser ignorados antes de uma execução do indexador falhar. Para obter mais informações, consulte [criar API](https://docs.microsoft.com/rest/api/searchservice/Create-Indexer)do indexador.

Talvez seja necessário permitir que os serviços do Azure se conectem ao seu banco de dados. Consulte [conectando do Azure](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) para obter instruções sobre como fazer isso.

Para monitorar o status do indexador e o histórico de execução (número de itens indexados, falhas, etc.), use uma solicitação de **status** do indexador:

    GET https://myservice.search.windows.net/indexers/myindexer/status?api-version=2019-05-06
    api-key: admin-key

A resposta deve ser semelhante ao seguinte:

    {
        "\@odata.context":"https://myservice.search.windows.net/$metadata#Microsoft.Azure.Search.V2015_02_28.IndexerExecutionInfo",
        "status":"running",
        "lastResult": {
            "status":"success",
            "errorMessage":null,
            "startTime":"2015-02-21T00:23:24.957Z",
            "endTime":"2015-02-21T00:36:47.752Z",
            "errors":[],
            "itemsProcessed":1599501,
            "itemsFailed":0,
            "initialTrackingState":null,
            "finalTrackingState":null
        },
        "executionHistory":
        [
            {
                "status":"success",
                "errorMessage":null,
                "startTime":"2015-02-21T00:23:24.957Z",
                "endTime":"2015-02-21T00:36:47.752Z",
                "errors":[],
                "itemsProcessed":1599501,
                "itemsFailed":0,
                "initialTrackingState":null,
                "finalTrackingState":null
            },
            ... earlier history items
        ]
    }

O histórico de execução contém até 50 das execuções concluídas mais recentemente, que são classificadas na ordem cronológica inversa (de forma que a execução mais recente venha primeiro na resposta).
Informações adicionais sobre a resposta podem ser encontradas em [obter status](https://go.microsoft.com/fwlink/p/?LinkId=528198) do indexador

## <a name="run-indexers-on-a-schedule"></a>Executar indexadores em uma agenda
Você também pode organizar o indexador para ser executado periodicamente em um agendamento. Para fazer isso, adicione a propriedade **Schedule** ao criar ou atualizar o indexador. O exemplo a seguir mostra uma solicitação PUT para atualizar o indexador:

    PUT https://myservice.search.windows.net/indexers/myindexer?api-version=2019-05-06
    Content-Type: application/json
    api-key: admin-key

    {
        "dataSourceName" : "myazuresqldatasource",
        "targetIndexName" : "target index name",
        "schedule" : { "interval" : "PT10M", "startTime" : "2015-01-01T00:00:00Z" }
    }

O parâmetro **Interval** é obrigatório. O intervalo refere-se ao tempo entre o início de duas execuções de indexador consecutivas. O menor intervalo permitido é de 5 minutos; o mais longo é um dia. Ele deve ser formatado como um valor XSD "dayTimeDuration" (um subconjunto restrito de um valor de [duração ISO 8601](https://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) ). O padrão para isso é: `P(nD)(T(nH)(nM))`. Exemplos: `PT15M` para cada 15 minutos, `PT2H` para cada 2 horas.

Para obter mais informações sobre como definir agendas do indexador, consulte [como agendar indexadores para Azure Search](search-howto-schedule-indexers.md).

<a name="CaptureChangedRows"></a>

## <a name="capture-new-changed-and-deleted-rows"></a>Capturar linhas novas, alteradas e excluídas

Azure Search usa a **indexação incremental** para evitar a reindexação de toda a tabela ou exibição toda vez que um indexador é executado. Azure Search fornece duas políticas de detecção de alteração para dar suporte à indexação incremental. 

### <a name="sql-integrated-change-tracking-policy"></a>Política de Controle de Alterações integrada do SQL
Se o banco de dados SQL der suporte ao [controle de alterações](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-tracking-sql-server), é recomendável usar a **política de controle de alterações integrada do SQL**. Essa é a política mais eficiente. Além disso, ele permite que Azure Search identifique as linhas excluídas sem a necessidade de adicionar uma coluna explícita de "exclusão reversível" à tabela.

#### <a name="requirements"></a>Requisitos 

+ Requisitos de versão do banco de dados:
  * SQL Server 2012 SP3 e posterior, se você estiver usando SQL Server em VMs do Azure.
  * Banco de dados SQL do Azure V12, se você estiver usando o banco de dados SQL do Azure.
+ Somente tabelas (sem exibições). 
+ No banco de dados, habilite o [controle de alterações](https://docs.microsoft.com/sql/relational-databases/track-changes/enable-and-disable-change-tracking-sql-server) para a tabela. 
+ Nenhuma chave primária composta (uma chave primária que contém mais de uma coluna) na tabela.  

#### <a name="usage"></a>Utilização

Para usar essa política, crie ou atualize sua fonte de dados da seguinte maneira:

    {
        "name" : "myazuresqldatasource",
        "type" : "azuresql",
        "credentials" : { "connectionString" : "connection string" },
        "container" : { "name" : "table or view name" },
        "dataChangeDetectionPolicy" : {
           "@odata.type" : "#Microsoft.Azure.Search.SqlIntegratedChangeTrackingPolicy"
      }
    }

Ao usar a política de controle de alterações integrada do SQL, não especifique uma política de detecção de exclusão de dados separada-essa política tem suporte interno para identificar linhas excluídas. No entanto, para que as exclusões sejam detectadas "automagicamente", a chave do documento no índice de pesquisa deve ser a mesma que a chave primária na tabela SQL. 

> [!NOTE]  
> Ao usar [TRUNCATE TABLE](https://docs.microsoft.com/sql/t-sql/statements/truncate-table-transact-sql) para remover um grande número de linhas de uma tabela SQL, o indexador precisa ser [redefinido](https://docs.microsoft.com/rest/api/searchservice/reset-indexer) para redefinir o estado de controle de alterações para selecionar exclusões de linha.

<a name="HighWaterMarkPolicy"></a>

### <a name="high-water-mark-change-detection-policy"></a>Política de detecção de alteração de marca d' água alta

Essa política de detecção de alteração depende de uma coluna de "marca d' água alta" capturando a versão ou a hora em que uma linha foi atualizada pela última vez. Se você estiver usando uma exibição, deverá usar uma política de marca d' água alta. A coluna de marca d' água alta deve atender aos seguintes requisitos.

#### <a name="requirements"></a>Requisitos 

* Todas as inserções especificam um valor para a coluna.
* Todas as atualizações de um item também alteram o valor da coluna.
* O valor dessa coluna aumenta com cada inserção ou atualização.
* Consultas com as cláusulas WHERE e ORDER BY a seguir podem ser executadas com eficiência:`WHERE [High Water Mark Column] > [Current High Water Mark Value] ORDER BY [High Water Mark Column]`

> [!IMPORTANT] 
> É altamente recomendável usar o tipo de dados do [Subversion](https://docs.microsoft.com/sql/t-sql/data-types/rowversion-transact-sql) para a coluna de marca d' água alta. Se qualquer outro tipo de dados for usado, o controle de alterações não terá garantia de capturar todas as alterações na presença de transações em execução simultaneamente com uma consulta do indexador. Ao usar o **Subversion** em uma configuração com réplicas somente leitura, você deve apontar o indexador na réplica primária. Somente uma réplica primária pode ser usada para cenários de sincronização de dados.

#### <a name="usage"></a>Utilização

Para usar uma política de marca d' água alta, crie ou atualize sua fonte de dados da seguinte maneira:

    {
        "name" : "myazuresqldatasource",
        "type" : "azuresql",
        "credentials" : { "connectionString" : "connection string" },
        "container" : { "name" : "table or view name" },
        "dataChangeDetectionPolicy" : {
           "@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
           "highWaterMarkColumnName" : "[a rowversion or last_updated column name]"
      }
    }

> [!WARNING]
> Se a tabela de origem não tiver um índice na coluna de marca d' água alta, as consultas usadas pelo indexador do SQL poderão atingir o tempo limite. Em particular, a `ORDER BY [High Water Mark Column]` cláusula requer que um índice seja executado com eficiência quando a tabela contiver muitas linhas.
>
>

Se você encontrar erros de tempo limite, poderá usar `queryTimeout` a definição de configuração do indexador para definir o tempo limite da consulta como um valor maior que o tempo limite padrão de 5 minutos. Por exemplo, para definir o tempo limite como 10 minutos, crie ou atualize o indexador com a seguinte configuração:

    {
      ... other indexer definition properties
     "parameters" : {
            "configuration" : { "queryTimeout" : "00:10:00" } }
    }

Você também pode desabilitar a `ORDER BY [High Water Mark Column]` cláusula. No entanto, isso não é recomendável porque se a execução do indexador for interrompida por um erro, o indexador precisará processar novamente todas as linhas se ele for executado mais tarde, mesmo que o indexador já tenha processado quase todas as linhas no momento em que foi interrompido. Para desabilitar a `ORDER BY` cláusula, use a `disableOrderByHighWaterMarkColumn` configuração na definição do indexador:  

    {
     ... other indexer definition properties
     "parameters" : {
            "configuration" : { "disableOrderByHighWaterMarkColumn" : true } }
    }

### <a name="soft-delete-column-deletion-detection-policy"></a>Política de detecção de exclusão de coluna de exclusão reversível
Quando as linhas são excluídas da tabela de origem, você provavelmente também deseja excluir essas linhas do índice de pesquisa. Se você usar a política de controle de alterações integrada do SQL, isso será resolvido para você. No entanto, a política de controle de alterações de marca d' água alta não ajuda com linhas excluídas. O que fazer?

Se as linhas forem fisicamente removidas da tabela, Azure Search não terá como inferir a presença de registros que não existem mais.  No entanto, você pode usar a técnica de "exclusão reversível" para excluir logicamente as linhas sem removê-las da tabela. Adicione uma coluna à sua tabela ou exiba e marque as linhas como excluídas usando essa coluna.

Ao usar a técnica de exclusão reversível, você pode especificar a política de exclusão reversível da seguinte maneira ao criar ou atualizar a fonte de dados:

    {
        …,
        "dataDeletionDetectionPolicy" : {
           "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
           "softDeleteColumnName" : "[a column name]",
           "softDeleteMarkerValue" : "[the value that indicates that a row is deleted]"
        }
    }

O **softDeleteMarkerValue** deve ser uma cadeia de caracteres – use a representação de cadeia de caracteres do seu valor real. Por exemplo, se você tiver uma coluna de inteiros em que as linhas excluídas são marcadas com `"1"`o valor 1, use. Se você tiver uma coluna de bits em que as linhas excluídas são marcadas com o valor true booliano `true`, use o literal `True` de cadeia de caracteres ou, caso contrário, não importa.

<a name="TypeMapping"></a>

## <a name="mapping-between-sql-and-azure-search-data-types"></a>Mapeamento entre os tipos de dados SQL e Azure Search
| Tipo de dados SQL | Tipos de campo de índice de destino permitidos | Notas |
| --- | --- | --- |
| bit |Edm.Boolean, Edm.String | |
| int, smallint, tinyint |Edm.Int32, Edm.Int64, Edm.String | |
| bigint |Edm.Int64, Edm.String | |
| real, float |EDM. Double, EDM. String | |
| smallmoney, numérico decimal do Money |Edm.String |Azure Search não dá suporte à conversão de tipos decimais em EDM. Double porque isso perderia a precisão |
| char, nchar, varchar, nvarchar |Edm.String<br/>Coleção (Edm.String) |Uma cadeia de caracteres SQL pode ser usada para preencher um campo de coleção (EDM. String) se a cadeia de caracteres representar uma matriz JSON de cadeias de caracteres:`["red", "white", "blue"]` |
| smalldatetime, DateTime, datetime2, Date, DateTimeOffset |Edm.DateTimeOffset, Edm.String | |
| uniqueidentifier |Edm.String | |
| gráfico |Edm.GeographyPoint |Somente instâncias de Geografia do tipo POINT com SRID 4326 (que é o padrão) têm suporte |
| rowversion |N/A |Colunas de versão de linha não podem ser armazenadas no índice de pesquisa, mas podem ser usadas para controle de alterações |
| tempo, TimeSpan, binary, varbinary, Image, XML, Geometry, tipos CLR |N/A |Não suportado |

## <a name="configuration-settings"></a>Definições de configuração
O indexador do SQL expõe várias definições de configuração:

| Definição | Tipo de dados | Objetivo | Valor predefinido |
| --- | --- | --- | --- |
| queryTimeout |Cadeia de caracteres |Define o tempo limite para a execução da consulta SQL |5 minutos ("00:05:00") |
| disableOrderByHighWaterMarkColumn |bool |Faz com que a consulta SQL usada pela política de marca d' água alta omita a cláusula ORDER BY. Consulte a [política de marca d' água alta](#HighWaterMarkPolicy) |false |

Essas configurações são usadas no `parameters.configuration` objeto na definição do indexador. Por exemplo, para definir o tempo limite da consulta como 10 minutos, crie ou atualize o indexador com a seguinte configuração:

    {
      ... other indexer definition properties
     "parameters" : {
            "configuration" : { "queryTimeout" : "00:10:00" } }
    }

## <a name="faq"></a>FAQ

**P: Posso usar o indexador do SQL Azure com bancos de dados SQL em execução em VMs IaaS no Azure?**

Sim. No entanto, você precisa permitir que o serviço de pesquisa se conecte ao seu banco de dados. Para obter mais informações, consulte [Configurar uma conexão de um Azure Search indexador para SQL Server em uma VM do Azure](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md).

**P: Posso usar o indexador do SQL do Azure com bancos de dados SQL em execução no local?**

Não diretamente. Não recomendamos nem damos suporte a uma conexão direta, pois isso exigiria que você abra seus bancos de dados para o tráfego da Internet. Os clientes tiveram êxito com esse cenário usando tecnologias de ponte como Azure Data Factory. Para obter mais informações, consulte [enviar dados por push para um índice de Azure Search usando Azure data Factory](https://docs.microsoft.com/azure/data-factory/data-factory-azure-search-connector).

**P: Posso usar o indexador do SQL Azure com bancos de dados diferentes de SQL Server em execução no IaaS no Azure?**

Não. Não há suporte para esse cenário, pois não testamos o indexador com nenhum banco de dados que não seja o SQL Server.  

**P: Posso criar vários indexadores em execução em um agendamento?**

Sim. No entanto, somente um indexador pode ser executado em um nó ao mesmo tempo. Se você precisar de vários indexadores em execução simultaneamente, considere escalar verticalmente o serviço de pesquisa para mais de uma unidade de pesquisa.

**P: A execução de um indexador afeta minha carga de trabalho de consulta?**

Sim. O indexador é executado em um dos nós no serviço de pesquisa, e os recursos desse nó são compartilhados entre a indexação e o fornecimento de tráfego de consulta e outras solicitações de API. Se você executar uma indexação intensiva e cargas de trabalho de consulta e encontrar uma alta taxa de erros 503 ou tempos de resposta crescentes, considere [escalar verticalmente o serviço de pesquisa](search-capacity-planning.md).

**P: Posso usar uma réplica secundária em um [cluster de failover](https://docs.microsoft.com/azure/sql-database/sql-database-geo-replication-overview) como uma fonte de dados?**

Depende. Para a indexação completa de uma tabela ou exibição, você pode usar uma réplica secundária. 

Para indexação incremental, o Azure Search dá suporte a duas políticas de detecção de alteração: Controle de alterações integrado do SQL e marca d' água alta.

Em réplicas somente leitura, o banco de dados SQL não dá suporte ao controle de alterações integrado. Portanto, você deve usar a política de marca d' água alta. 

Nossa recomendação padrão é usar o tipo de dados do Subversion para a coluna de marca d' água alta. No entanto, o uso do timeversion depende da `MIN_ACTIVE_ROWVERSION` função do banco de dados SQL, que não tem suporte em réplicas somente leitura. Portanto, você deve apontar o indexador para uma réplica primária se estiver usando o Subversion.

Se você tentar usar o Subversion em uma réplica somente leitura, você verá o seguinte erro: 

    "Using a rowversion column for change tracking is not supported on secondary (read-only) availability replicas. Please update the datasource and specify a connection to the primary availability replica.Current database 'Updateability' property is 'READ_ONLY'".

**P: Posso usar uma coluna alternativa, que não seja de uma versão para o controle de alterações de marca d' água alta?**

Isso não é recomendado. Somente o **Subversion** permite a sincronização de dados confiável. No entanto, dependendo da lógica do aplicativo, pode ser seguro se:

+ Você pode garantir que, quando o indexador for executado, não haja transações pendentes na tabela que está sendo indexada (por exemplo, todas as atualizações de tabela acontecem como um lote em um agendamento, e a Azure Search agenda do indexador está definida para evitar sobreposição com a atualização da tabela agenda).  

+ Periodicamente, você faz um reindexação completa para selecionar todas as linhas perdidas. 