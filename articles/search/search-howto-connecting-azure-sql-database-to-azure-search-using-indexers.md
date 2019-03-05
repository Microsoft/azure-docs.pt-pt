---
title: Ligar e indexar o Azure SQL Database conteúdo com indexadores - Azure Search
description: Aprenda a pesquisar dados na base de dados do SQL Azure com indexadores para pesquisa em texto completo no Azure Search. Este artigo aborda as ligações, a configuração do indexador e ingestão de dados.
ms.date: 03/01/2019
author: mgottein
manager: cgronlun
ms.author: magottei
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seodec2018
ms.openlocfilehash: 5453bcdd371c0639cb1d3568f05a1768e6204d3d
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2019
ms.locfileid: "57315219"
---
# <a name="connect-to-and-index-azure-sql-database-content-using-azure-search-indexers"></a>Ligar a e indexar o conteúdo de indexadores do Azure Search a utilizar o Azure SQL Database

Antes de pode consultar um [índice da Azure Search](search-what-is-an-index.md), deve preenchê-lo com os seus dados. Se os dados residem numa base de dados SQL do Azure, um **indexador de Azure Search para o Azure SQL Database** (ou **indexador de SQL do Azure** para abreviar) pode automatizar o processo de indexação, o que significa menos código para escrever e menos infraestrutura preocupar.

Este artigo aborda a mecânica do uso [indexadores](search-indexer-overview.md), mas também descreve as funcionalidades só estão disponíveis com bases de dados SQL do Azure (por exemplo, registo de alterações integrado). 

Além de bancos de dados SQL do Azure, o Azure Search fornece indexadores para [do Azure Cosmos DB](search-howto-index-cosmosdb.md), [armazenamento de Blobs do Azure](search-howto-indexing-azure-blob-storage.md), e [armazenamento de tabelas do Azure](search-howto-indexing-azure-tables.md). Para pedir suporte para outras origens de dados, fornecer seus comentários sobre o [fórum de comentários do Azure Search](https://feedback.azure.com/forums/263029-azure-search/).

## <a name="indexers-and-data-sources"></a>Indexadores e origens de dados

R **origem de dados** Especifica quais os dados para o índice, as credenciais de acesso a dados e as políticas que identificam com eficiência as alterações nos dados (novas, modificadas ou eliminadas linhas). Ele é definido como um recurso independente para que possa ser utilizado por vários indexadores.

Uma **indexador** é um recurso que se liga uma única origem de dados com um índice de pesquisa direcionada. Um indexador é usado das seguintes formas:

* Efetue uma cópia única dos dados para preencher um índice.
* Atualize um índice com as alterações na origem de dados com base numa agenda.
* Execute a pedido para atualizar um índice, conforme necessário.

Um indexador único só pode consumir uma tabela ou vista, mas pode criar vários indexadores, se deseja preencher vários índices de pesquisa. Para obter mais informações sobre conceitos, consulte [operações do indexador: Fluxo de trabalho típico](https://docs.microsoft.com/rest/api/searchservice/Indexer-operations#typical-workflow).

Pode configurar e configurar um indexador de SQL do Azure com:

* No Assistente para importar dados a [portal do Azure](https://portal.azure.com)
* O Azure Search [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer?view=azure-dotnet)
* O Azure Search [REST API](https://docs.microsoft.com/rest/api/searchservice/indexer-operations)

Neste artigo, vamos utilizar a API REST para criar **indexadores** e **origens de dados**.

## <a name="when-to-use-azure-sql-indexer"></a>Quando utilizar o indexador de SQL do Azure
Dependendo de vários fatores relacionados aos seus dados, a utilização do indexador de SQL do Azure pode ou não ser adequada. Se seus dados se enquadrar os seguintes requisitos, pode usar o indexador de SQL do Azure.

| Critérios | Detalhes |
|----------|---------|
| Dados são originados por uma única tabela ou vista | Se os dados estiverem espalhados entre várias tabelas, pode criar uma única vista dos dados. No entanto, se utilizar um modo de exibição, não será possível utilizar a deteção de alterações integrado do SQL Server para atualizar um índice com as alterações incrementais. Para obter mais informações, consulte [capturando alterado e eliminar linhas](#CaptureChangedRows) abaixo. |
| Tipos de dados são compatíveis | A maioria dos mas não todos os tipos de SQL são suportados num índice da Azure Search. Para obter uma lista, consulte [tipos de dados de mapeamento](#TypeMapping). |
| Não é necessária a sincronização de dados em tempo real | Um indexador pode reindexar sua tabela no máximo a cada cinco minutos. Se precisam de seus dados mudam com freqüência e as alterações sejam refletidas no índice em segundos ou minutos únicos, recomendamos que utilize o [REST API](https://docs.microsoft.com/rest/api/searchservice/AddUpdate-or-Delete-Documents) ou [SDK do .NET](search-import-data-dotnet.md) para enviar por push de linhas atualizadas diretamente. |
| A indexação incremental é possível | Se tiver um grande conjunto de dados e o plano para executar o indexador com base numa agenda, Azure Search tem de ser capaz de identificar com eficiência novas, alteradas ou eliminadas linhas. A indexação não incremental só é permitida se estiver a indexação a pedido (não numa agenda) ou, menos de 100 000 linhas de indexação. Para obter mais informações, consulte [capturando alterado e eliminar linhas](#CaptureChangedRows) abaixo. |

> [!NOTE] 
> O Azure Search oferece suporte apenas a autenticação do SQL Server. Se necessitar de suporte para a autenticação do Azure Active Directory palavra-passe, votar, para que isso [sugestão de UserVoice](https://feedback.azure.com/forums/263029-azure-search/suggestions/33595465-support-azure-active-directory-password-authentica).

## <a name="create-an-azure-sql-indexer"></a>Criar um indexador SQL do Azure

1. Crie a origem de dados:

   ```
    POST https://myservice.search.windows.net/datasources?api-version=2017-11-11
    Content-Type: application/json
    api-key: admin-key

    {
        "name" : "myazuresqldatasource",
        "type" : "azuresql",
        "credentials" : { "connectionString" : "Server=tcp:<your server>.database.windows.net,1433;Database=<your database>;User ID=<your user name>;Password=<your password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30;" },
        "container" : { "name" : "name of the table or view that you want to index" }
    }
   ```

   Pode obter a cadeia de ligação do [portal do Azure](https://portal.azure.com); utilize o `ADO.NET connection string` opção.

2. Se ainda não tiver uma, crie o índice de pesquisa do Azure de destino. Pode criar um índice com o [portal](https://portal.azure.com) ou o [criar o índice API](https://docs.microsoft.com/rest/api/searchservice/Create-Index). Certifique-se de que o esquema do seu índice de destino é compatível com o esquema da tabela de origem - consulte [mapeamento entre o SQL e o Azure search, os tipos de dados](#TypeMapping).

3. Crie o indexador, dando a ele um nome e o índice de origem e destino de dados de referência:

    ```
    POST https://myservice.search.windows.net/indexers?api-version=2017-11-11
    Content-Type: application/json
    api-key: admin-key

    {
        "name" : "myindexer",
        "dataSourceName" : "myazuresqldatasource",
        "targetIndexName" : "target index name"
    }
    ```

Um indexador criado dessa forma, não tem uma agenda. Ele é automaticamente executada uma vez quando é criado. Pode executá-lo novamente a qualquer momento, utilizando um **executar o indexador** pedido:

    POST https://myservice.search.windows.net/indexers/myindexer/run?api-version=2017-11-11
    api-key: admin-key

Pode personalizar vários aspectos do comportamento de indexador, como o tamanho de lote e podem ser ignorados o número de documentos antes de falha de execução de um indexador. Para obter mais informações, consulte [API do indexador de criar](https://docs.microsoft.com/rest/api/searchservice/Create-Indexer).

Se pretender permitir que os serviços do Azure ligar à base de dados. Ver [ligar a partir de Azure](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) para obter instruções sobre como fazer isso.

Para monitorizar o histórico de estado e a execução do indexador (número de itens indexados, falhas, etc.), utilize um **estado de indexador** pedido:

    GET https://myservice.search.windows.net/indexers/myindexer/status?api-version=2017-11-11
    api-key: admin-key

A resposta deve ter um aspeto semelhante ao seguinte:

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

Histórico de execução contém até 50 das execuções concluídas, que são classificadas em ordem cronológica reversa (para que a execução mais recente acontecer primeira na resposta).
Informações adicionais sobre a resposta podem ser encontradas no [obter estado do indexador](https://go.microsoft.com/fwlink/p/?LinkId=528198)

## <a name="run-indexers-on-a-schedule"></a>Executar os indexadores com base numa agenda
Também é possível providenciar o indexador para executar periodicamente com base numa agenda. Para tal, adicione a **agenda** propriedade quando criar ou atualizar o indexador. O exemplo abaixo mostra um pedido PUT para atualizar o indexador:

    PUT https://myservice.search.windows.net/indexers/myindexer?api-version=2017-11-11
    Content-Type: application/json
    api-key: admin-key

    {
        "dataSourceName" : "myazuresqldatasource",
        "targetIndexName" : "target index name",
        "schedule" : { "interval" : "PT10M", "startTime" : "2015-01-01T00:00:00Z" }
    }

O **intervalo** é necessário o parâmetro. O intervalo de refere-se para o tempo entre o início de duas execuções de indexador consecutivos. O intervalo menor permitido é de 5 minutos; há mais tempo é um dia. Tem de ser formatado como um valor de "dayTimeDuration" XSD (um subconjunto restrito de um [duração ISO 8601](https://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) valor). O padrão para isto é: `P(nD)(T(nH)(nM))`. Exemplos: `PT15M` para cada 15 minutos, `PT2H` para cada 2 horas.

O opcional **startTime** indica quando as execuções agendadas devem começar. Se for omitido, é utilizada a hora UTC atual. Desta vez pode ser no passado – nesse caso a primeira execução está agendado como se estiver a ser executado o indexador continuamente desde a startTime.  

Pode executar apenas uma execução de um indexador de cada vez. Se um indexador está em execução quando sua execução estiver agendada, a execução será adiada até que a próxima hora de agendada.

Vamos considerar um exemplo para tornar isso mais concreto. Suponha que estamos a agenda de hora a hora seguinte configurada:

    "schedule" : { "interval" : "PT1H", "startTime" : "2015-03-01T00:00:00Z" }

Eis o que acontece:

1. A primeira execução do indexador começa em ou em qualquer parte 1 de Março de 2015 12 horas UTC.
2. Assumir que esta execução demora 20 minutos (ou em qualquer altura inferior a 1 hora).
3. A segunda execução começa em ou em qualquer parte 1 de Março de 2015 às 1:00
4. Agora suponha que esta execução demora mais de uma hora – por exemplo, 70 minutos, para que o processo estar concluído aproximadamente 2: 10h
5. É agora o tempo de 2 horas para a execução de terceiro iniciar. No entanto, uma vez que a segunda execução de 1H ainda está em execução, a execução de terceiro foi ignorada. A execução de terceiro começa às 3 horas da manhã

Pode adicionar, alterar ou eliminar um agendamento para um indexador existente com uma **PUT indexador** pedido.

<a name="CaptureChangedRows"></a>

## <a name="capture-new-changed-and-deleted-rows"></a>Captura de nova, alteração e eliminação de linhas

Utiliza o Azure Search **indexação incremental** para evitar a reindexar a tabela inteira ou ver sempre que um indexador é executado. O Azure Search fornece que dois alterar políticas de deteção para oferecer suporte a indexação incremental. 

### <a name="sql-integrated-change-tracking-policy"></a>Política de controlo de alterações integrado do SQL
Se a sua base de dados SQL suporta [controlo de alterações](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-tracking-sql-server), recomendamos que utilize **SQL integrada Alterar controlo política**. Esta é a política mais eficiente. Além disso, ele permite que Azure Search identificar linhas eliminadas sem ter de voltar a adicionar uma coluna de "eliminação de forma recuperável" explícito à sua tabela.

#### <a name="requirements"></a>Requisitos 

+ Requisitos de versão da base de dados:
  * SQL Server 2012 SP3 e posterior, se estiver a utilizar o SQL Server em VMs do Azure.
  * O Azure SQL Database V12, se estiver a utilizar a base de dados do Azure SQL.
+ Tabelas apenas (não existem vistas). 
+ Na base de dados, [ativar o controlo de alterações](https://docs.microsoft.com/sql/relational-databases/track-changes/enable-and-disable-change-tracking-sql-server) para a tabela. 
+ Nenhuma composto chave primária (uma primária chave que contém mais do que uma coluna) na tabela.  

#### <a name="usage"></a>Utilização

Para utilizar esta política, criar ou atualizar a sua origem de dados como este:

    {
        "name" : "myazuresqldatasource",
        "type" : "azuresql",
        "credentials" : { "connectionString" : "connection string" },
        "container" : { "name" : "table or view name" },
        "dataChangeDetectionPolicy" : {
           "@odata.type" : "#Microsoft.Azure.Search.SqlIntegratedChangeTrackingPolicy"
      }
    }

Quando utilizar a política, de controlo de alterações integrado do SQL não especificar uma política de deteção de eliminação de dados separado - esta política não tem suporte incorporado para identificar eliminar linhas. No entanto, para exclusões ser detetado "automagicamente", a chave do documento no seu índice de pesquisa tem de ser o mesmo que a chave primária na tabela de SQL. 

> [!NOTE]  
> Ao usar [TRUNCATE TABLE](https://docs.microsoft.com/sql/t-sql/statements/truncate-table-transact-sql) para remover um grande número de linhas de uma tabela SQL, o indexador tem de ser [repor](https://docs.microsoft.com/rest/api/searchservice/reset-indexer) para repor a Estado de retirada eliminações de linha de controlo de alterações.

<a name="HighWaterMarkPolicy"></a>

### <a name="high-water-mark-change-detection-policy"></a>Política de deteção de alteração do máximo

Esta política de deteção de alteração se baseia numa coluna de "máximo" capturar a versão ou a hora quando uma linha foi atualizado pela última vez. Se estiver a utilizar um modo de exibição, tem de utilizar uma política de máximo. A coluna de máximo tem de cumprir os seguintes requisitos.

#### <a name="requirements"></a>Requisitos 

* Todas as inserções de especificar um valor para a coluna.
* Todas as atualizações para um item também alterar o valor da coluna.
* O valor desta coluna aumenta com cada inserção ou atualização.
* As consultas com o seguinte onde e cláusulas ORDER BY podem ser executadas com eficiência: `WHERE [High Water Mark Column] > [Current High Water Mark Value] ORDER BY [High Water Mark Column]`

> [!IMPORTANT] 
> Recomendamos vivamente a utilização a [rowversion](https://docs.microsoft.com/sql/t-sql/data-types/rowversion-transact-sql) tipo de dados para a coluna de marca de água alta. Se for utilizado qualquer outro tipo de dados, o controlo de alterações não é garantida para capturar todas as alterações na presença de transações em execução simultaneamente com uma consulta de indexador. Ao usar **rowversion** numa configuração com réplicas só de leitura, tem de apontar o indexador para a réplica primária. Apenas uma réplica primária pode ser utilizada para cenários de sincronização de dados.

#### <a name="usage"></a>Utilização

Para utilizar uma política de máximo, criar ou atualizar a sua origem de dados como este:

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
> Se a tabela de origem não tiver um índice na coluna máximo, o indexador de SQL de consultas podem tempo limite. Em particular, o `ORDER BY [High Water Mark Column]` cláusula requer um índice para ser executada com eficiência quando a tabela contém o número de linhas.
>
>

Se encontrar erros de tempo limite, pode utilizar o `queryTimeout` definição de configuração do indexador para definir o tempo limite de consulta para um valor maior do que o tempo de limite de 5 minutos do padrão. Por exemplo, para definir o tempo limite de 10 minutos, criar ou atualizar o indexador com a seguinte configuração:

    {
      ... other indexer definition properties
     "parameters" : {
            "configuration" : { "queryTimeout" : "00:10:00" } }
    }

Também pode desativar o `ORDER BY [High Water Mark Column]` cláusula. No entanto, isto não é recomendado porque se a execução do indexador é interrompida devido a um erro, o indexador tem de processar novamente todas as linhas se for executado mais tarde - mesmo que o indexador já processou quase todas as linhas no momento que foi interrompida. Para desativar a `ORDER BY` cláusula, utilize o `disableOrderByHighWaterMarkColumn` definição na definição de indexador:  

    {
     ... other indexer definition properties
     "parameters" : {
            "configuration" : { "disableOrderByHighWaterMarkColumn" : true } }
    }

### <a name="soft-delete-column-deletion-detection-policy"></a>Política de deteção de eliminação de coluna eliminar de forma recuperável
Quando as linhas são eliminadas da tabela de origem, provavelmente pretende eliminar as linhas do também o índice de pesquisa. Se utilizar a política de controlo de alterações integrado do SQL, isso é resolvido para. No entanto, a política de controlo de alterações de máximo não ajudá-lo com linhas eliminadas. O que fazer?

Se as linhas são fisicamente removidas da tabela, o Azure Search tem nenhuma forma para inferir a presença de registos que já não existe.  No entanto, pode utilizar a técnica de "eliminação de forma recuperável" logicamente eliminar linhas sem removê-los a partir da tabela. Adicione uma coluna para suas linhas de tabela ou vista e marca como eliminado a utilização dessa coluna.

Ao usar a técnica de eliminação de forma recuperável, pode especificar a eliminação de forma recuperável política da seguinte forma quando criar ou atualizar a origem de dados:

    {
        …,
        "dataDeletionDetectionPolicy" : {
           "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
           "softDeleteColumnName" : "[a column name]",
           "softDeleteMarkerValue" : "[the value that indicates that a row is deleted]"
        }
    }

O **softDeleteMarkerValue** deve ser uma cadeia de caracteres – utilize a representação de cadeia de caracteres de seu valor real. Por exemplo, se tiver uma coluna de números inteiros em que as linhas eliminadas são marcadas com o valor de 1, utilize `"1"`. Se tiver uma coluna BIT em que as linhas eliminadas são marcadas com o valor true booleano, utilize a cadeia de caracteres literal `True` ou `true`, não importa o caso.

<a name="TypeMapping"></a>

## <a name="mapping-between-sql-and-azure-search-data-types"></a>Mapeamento entre tipos de dados SQL e o Azure Search
| Tipo de dados SQL | Permitidos os tipos de campo de índice de destino | Notas |
| --- | --- | --- |
| bit |Edm.Boolean, Edm.String | |
| int, smallint, tinyint |Edm.Int32, Edm.Int64, Edm.String | |
| bigint |Edm.Int64, Edm.String | |
| número de vírgula flutuante real, |Edm.Double, Edm.String | |
| smallmoney, numérica decimal de dinheiro |Edm.String |O Azure Search não suporta a conversão de tipos de decimais em Edm.Double porque isso perderia precisão |
| char, nchar, varchar, nvarchar |Edm.String<br/>Coleção (Edm.String) |Uma cadeia de caracteres SQL pode ser utilizada para preencher um campo de Collection(Edm.String) se a cadeia de caracteres representa uma matriz JSON de cadeias de caracteres: `["red", "white", "blue"]` |
| smalldatetime, datetime, datetime2, date, datetimeoffset |Edm.DateTimeOffset, Edm.String | |
| uniqueidentifer |Edm.String | |
| Geografia |Edm.GeographyPoint |São suportadas apenas as instâncias de geografia do tipo POINT com 4326 SRID (que é a predefinição) |
| ROWVERSION |N/A |Colunas de versão de linha não podem ser armazenadas no índice de pesquisa, mas pode ser utilizados para controlo de alterações |
| tempo, período de tempo, binary, varbinary, imagem, xml, geometry, tipos CLR |N/A |Não suportado |

## <a name="configuration-settings"></a>Definições de configuração
Indexador SQL expõe várias definições de configuração:

| Definição | Tipo de dados | Objetivo | Valor predefinido |
| --- | --- | --- | --- |
| queryTimeout |string |Define o tempo limite de execução da consulta SQL |5 minutos ("00: 05:00") |
| disableOrderByHighWaterMarkColumn |booleano |Faz com que a consulta SQL utilizada pela política máximo para omita a cláusula ORDER BY. Consulte [máximo política](#HighWaterMarkPolicy) |false |

Estas definições são utilizadas no `parameters.configuration` objeto na definição do indexador. Por exemplo, para definir o tempo limite de consulta para 10 minutos, criar ou atualizar o indexador com a seguinte configuração:

    {
      ... other indexer definition properties
     "parameters" : {
            "configuration" : { "queryTimeout" : "00:10:00" } }
    }

## <a name="faq"></a>FAQ

**P: Pode utilizar o indexador de SQL do Azure com bases de dados SQL em execução em VMs de IaaS no Azure?**

Sim. No entanto, terá de permitir que o serviço de pesquisa ligar à base de dados. Para obter mais informações, consulte [configurar uma ligação a partir de um indexador de Azure Search para o SQL Server numa VM do Azure](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md).

**P: Pode utilizar o indexador de SQL do Azure com bases de dados SQL em execução no local?**

Não diretamente. Não é recomendável ou suportar uma conexão direta, como o fazer por isso exigiria a abertura de seus bancos de dados ao tráfego da Internet. Os clientes têm efetuada com êxito com este cenário através de tecnologias de ponte, como o Azure Data Factory. Para obter mais informações, consulte [enviar dados por Push para um índice da Azure Search utilizando o Azure Data Factory](https://docs.microsoft.com/azure/data-factory/data-factory-azure-search-connector).

**P: Pode utilizar o indexador de SQL do Azure com bancos de dados diferentes do SQL Server em execução no IaaS no Azure?**

Não. Não é suportada neste cenário, porque ainda não Testamos o indexador com quaisquer bases de dados diferentes do SQL Server.  

**P: Pode criar vários indexadores em execução com base numa agenda?**

Sim. No entanto, apenas um indexador pode estar em execução num nó em simultâneo. Se precisar de vários indexadores em execução em simultâneo, considere aumentar verticalmente o seu serviço de pesquisa mais do que uma unidade de pesquisa.

**P: Executar um indexador afeta a minha carga de trabalho de consulta?**

Sim. Indexador é executado em um de nós no seu serviço de pesquisa e recursos desse nó são partilhados entre a indexação e que serve o tráfego de consulta e de outros pedidos de API. Se executar cargas de trabalho intensivas de indexação e consulta e encontra uma alta taxa de 503 erros ou tempos de resposta de cada vez maior, considere [aumente verticalmente o seu serviço de pesquisa](search-capacity-planning.md).

**P: Posso utilizar uma réplica secundária num [cluster de ativação pós-falha](https://docs.microsoft.com/azure/sql-database/sql-database-geo-replication-overview) como uma origem de dados?**

Depende. Para indexação completo de uma tabela ou vista, pode utilizar uma réplica secundária. 

Para a indexação incremental, o Azure Search suporta duas políticas de deteção de alteração: SQL integrado do controle de alterações e máximo.

Em réplicas só de leitura, base de dados SQL não suporta o controlo de alterações integrado. Por conseguinte, tem de utilizar a política de máximo. 

A nossa recomendação padrão é usar o tipo de dados rowversion para a coluna de marca de água alta. No entanto, usar rowversion baseia-se do banco de dados de SQL `MIN_ACTIVE_ROWVERSION` função, o que não é suportada em réplicas só de leitura. Por conseguinte, tem de apontar o indexador para uma réplica primária se estiver a utilizar rowversion.

Se tentar usar rowversion numa réplica só de leitura, verá o seguinte erro: 

    "Using a rowversion column for change tracking is not supported on secondary (read-only) availability replicas. Please update the datasource and specify a connection to the primary availability replica.Current database 'Updateability' property is 'READ_ONLY'".

**P: Pode utilizar uma coluna não rowversion alternativa, para controlo de alterações máximo?**

Não é recomendado. Apenas **rowversion** permite a sincronização de dados fiável. No entanto, dependendo de sua lógica de aplicação, poderá ser seguro se:

+ Pode garantir que quando o indexador é executado, não há nenhuma transação pendentes na tabela que está a ser indexada (por exemplo, todas as atualizações de tabela acontecem como um lote com base numa agenda, e o agendamento do indexador de Azure Search está definido para evitar a sobreposição com a atualização de tabela agenda).  

+ Periodicamente, fazer uma reindex completa de retirada quaisquer linhas em falta. 