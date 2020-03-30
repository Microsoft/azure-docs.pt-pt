---
title: Pesquisar dados Do Azure SQL
titleSuffix: Azure Cognitive Search
description: Importar dados da Base de Dados Azure SQL utilizando indexadores, para pesquisa completa de texto em Pesquisa Cognitiva Azure. Este artigo abrange ligações, configuração do indexante e ingestão de dados.
manager: nitinme
author: mgottein
ms.author: magottei
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: c09727e8d92a449b41124eae6ad8381d66cb2619
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74113299"
---
# <a name="connect-to-and-index-azure-sql-database-content-using-an-azure-cognitive-search-indexer"></a>Conecte e indexe o conteúdo da base de dados Azure SQL utilizando um indexador de pesquisa cognitiva Azure

Antes de poder consultar um índice de [Pesquisa Cognitiva Azure,](search-what-is-an-index.md)deve povoá-lo com os seus dados. Se os dados viverem numa base de dados Azure SQL, um indexante de pesquisa cognitiva Azure para a Base de **Dados Azure SQL** (ou **indexador Azure SQL** para abreviar) pode automatizar o processo de indexação, o que significa menos código para escrever e menos infraestrutura para se preocupar.

Este artigo abrange a mecânica da utilização de [indexadores,](search-indexer-overview.md)mas também descreve funcionalidades apenas disponíveis com bases de dados Azure SQL (por exemplo, rastreio integrado de alterações). 

Além das bases de dados Azure SQL, a Azure Cognitive Search fornece indexadores para o armazenamento de [mesa SQB Azure Cosmos,](search-howto-index-cosmosdb.md) [Azure Blob](search-howto-indexing-azure-blob-storage.md)e armazenamento de [mesa Azure.](search-howto-indexing-azure-tables.md) Para solicitar apoio a outras fontes de dados, forneça o seu feedback sobre o fórum de feedback da [Pesquisa Cognitiva Azure](https://feedback.azure.com/forums/263029-azure-search/).

## <a name="indexers-and-data-sources"></a>Indexadores e fontes de dados

Uma fonte de **dados** especifica quais os dados indexados, credenciais para acesso a dados e políticas que identificam eficientemente as alterações nos dados (novas linhas modificadas ou eliminadas). É definido como um recurso independente para que possa ser usado por vários indexantes.

Um **indexante** é um recurso que liga uma única fonte de dados a um índice de pesquisa direcionado. Um indexante é utilizado das seguintes formas:

* Execute uma cópia única dos dados para povoar um índice.
* Atualize um índice com alterações na fonte de dados num horário.
* Executar a pedido para atualizar um índice conforme necessário.

Um único indexante só pode consumir uma tabela ou vista, mas pode criar vários indexantes se quiser preencher vários índices de pesquisa. Para obter mais informações sobre conceitos, consulte [Indexer Operations: Fluxo de trabalho típico](https://docs.microsoft.com/rest/api/searchservice/Indexer-operations#typical-workflow).

Pode configurar e configurar um indexador Azure SQL utilizando:

* Importador de dados assistente no [portal Azure](https://portal.azure.com)
* Pesquisa Cognitiva Azure [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer?view=azure-dotnet)
* [API de](https://docs.microsoft.com/rest/api/searchservice/indexer-operations) pesquisa cognitiva azure

Neste artigo, usaremos a API REST para criar **indexadores** e fontes de **dados.**

## <a name="when-to-use-azure-sql-indexer"></a>Quando utilizar o Indexante Azure SQL
Dependendo de vários fatores relacionados com os seus dados, a utilização do indexante Azure SQL pode ou não ser adequada. Se os seus dados se adequarem aos seguintes requisitos, pode utilizar o indexador Azure SQL.

| Critérios | Detalhes |
|----------|---------|
| Os dados têm origem numa única tabela ou vista | Se os dados estiverem espalhados por várias tabelas, pode criar uma única visão dos dados. No entanto, se utilizar uma vista, não poderá utilizar a deteção integrada de alterações integrada sQL Server para refrescar um índice com alterações incrementais. Para mais informações, consulte [As Linhas Alteradas e Apagadas](#CaptureChangedRows) de Capturing abaixo. |
| Os tipos de dados são compatíveis | A maioria, mas nem todos os tipos DeQL são suportados num índice de Pesquisa Cognitiva Azure. Para uma lista, consulte os tipos de [dados de mapeamento](#TypeMapping). |
| A sincronização de dados em tempo real não é necessária | Um indexante pode reindexar a sua mesa no máximo a cada cinco minutos. Se os seus dados mudarem frequentemente e as alterações precisarem de ser refletidas no índice em segundos ou minutos individuais, recomendamos a utilização do [REST API](https://docs.microsoft.com/rest/api/searchservice/AddUpdate-or-Delete-Documents) ou [.NET SDK](search-import-data-dotnet.md) para empurrar diretamente as linhas atualizadas. |
| A indexação incremental é possível | Se tiver um grande conjunto de dados e planear executar o indexador numa programação, a Pesquisa Cognitiva Azure deve ser capaz de identificar eficientemente novas linhas, alteradas ou apagadas. A indexação não incremental só é permitida se estiver a indexar a procura (não no horário) ou a indexar menos de 100.000 linhas. Para mais informações, consulte [As Linhas Alteradas e Apagadas](#CaptureChangedRows) de Capturing abaixo. |

> [!NOTE] 
> A Pesquisa Cognitiva Azure suporta apenas a autenticação do Servidor SQL. Se necessitar de suporte para autenticação de palavra-passe de Diretório Ativo Azure, por favor vote nesta [sugestão userVoice](https://feedback.azure.com/forums/263029-azure-search/suggestions/33595465-support-azure-active-directory-password-authentica).

## <a name="create-an-azure-sql-indexer"></a>Criar um Indexer Azure SQL

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

   Pode obter a cadeia de ligação do [portal Azure;](https://portal.azure.com) usar `ADO.NET connection string` a opção.

2. Crie o índice de pesquisa cognitiva Azure alvo se ainda não tiver um. Pode criar um índice utilizando o [portal](https://portal.azure.com) ou a [Create Index API](https://docs.microsoft.com/rest/api/searchservice/Create-Index). Certifique-se de que o esquema do seu índice-alvo é compatível com o esquema da tabela de origem - consulte o mapeamento entre os tipos de dados de [pesquisa cognitiva SQL e Azure](#TypeMapping).

3. Crie o indexador dando-lhe um nome e referindo a fonte de dados e o índice-alvo:

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

Um indexante criado desta forma não tem um horário. Funciona automaticamente uma vez quando é criado. Pode executá-lo novamente a qualquer momento usando um pedido **de indexador de execução:**

    POST https://myservice.search.windows.net/indexers/myindexer/run?api-version=2019-05-06
    api-key: admin-key

Você pode personalizar vários aspetos do comportamento indexador, tais como o tamanho do lote e quantos documentos podem ser ignorados antes que uma execução indexante falhe. Para mais informações, consulte [Create Indexer API](https://docs.microsoft.com/rest/api/searchservice/Create-Indexer).

Pode ser necessário permitir que os serviços do Azure se conectem à sua base de dados. Consulte [a ligação do Azure](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) para obter instruções sobre como fazê-lo.

Para monitorizar o estado do indexante e o histórico de execução (número de itens indexados, falhas, etc.), utilize um pedido de **estado indexado:**

    GET https://myservice.search.windows.net/indexers/myindexer/status?api-version=2019-05-06
    api-key: admin-key

A resposta deve ser semelhante à seguinte:

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

O histórico de execução contém até 50 das execuções mais recentemente concluídas, que estão classificadas na ordem cronológica inversa (de modo que a última execução vem em primeiro lugar na resposta).
Informações adicionais sobre a resposta podem ser encontradas no [Estatuto do Indexante Get](https://go.microsoft.com/fwlink/p/?LinkId=528198)

## <a name="run-indexers-on-a-schedule"></a>Executar indexadores em um horário
Também pode organizar o indexante para funcionar periodicamente num horário. Para isso, adicione a propriedade de **horário** ao criar ou atualizar o indexante. O exemplo abaixo mostra um pedido de PUT para atualizar o indexante:

    PUT https://myservice.search.windows.net/indexers/myindexer?api-version=2019-05-06
    Content-Type: application/json
    api-key: admin-key

    {
        "dataSourceName" : "myazuresqldatasource",
        "targetIndexName" : "target index name",
        "schedule" : { "interval" : "PT10M", "startTime" : "2015-01-01T00:00:00Z" }
    }

É necessário o parâmetro de **intervalo.** O intervalo refere-se ao tempo entre o início de duas execuções consecutivas do indexante. O menor intervalo permitido é de 5 minutos; o mais longo é um dia. Deve ser formatado como um valor XSD "daytimeduration" (um subconjunto restrito de um valor de [duração ISO 8601).](https://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) O padrão para `P(nD)(T(nH)(nM))`isto é: . Exemplos: `PT15M` por cada 15 minutos, `PT2H` por cada 2 horas.

Para obter mais informações sobre a definição de horários indexantes, consulte [como agendar indexadores para pesquisa cognitiva azure](search-howto-schedule-indexers.md).

<a name="CaptureChangedRows"></a>

## <a name="capture-new-changed-and-deleted-rows"></a>Capturar novas, alteradas e linhas apagadas

A Pesquisa Cognitiva Azure utiliza **um indexante incremental** para evitar ter de reindexar toda a tabela ou visualizar cada vez que um indexante corre. A Azure Cognitive Search fornece duas políticas de deteção de alterações para suportar a indexação incremental. 

### <a name="sql-integrated-change-tracking-policy"></a>Política integrada de rastreio de mudança sql
Se a sua base de dados SQL suportar o rastreio de [alterações,](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-tracking-sql-server)recomendamos a utilização da Política Integrada de **Rastreio de Alterações SQL**. Esta é a política mais eficiente. Além disso, permite que a Pesquisa Cognitiva Azure identifique linhas apagadas sem ter de adicionar uma coluna explícita de "soft delete" à sua tabela.

#### <a name="requirements"></a>Requisitos 

+ Requisitos de versão de base de dados:
  * SQL Server 2012 SP3 e mais tarde, se estiver a utilizar o SQL Server em VMs Azure.
  * Base de dados Azure SQL V12, se estiver a utilizar a Base de Dados Azure SQL.
+ Apenas mesas (sem vistas). 
+ Na base de dados, ative o rastreio de [alterações](https://docs.microsoft.com/sql/relational-databases/track-changes/enable-and-disable-change-tracking-sql-server) para a tabela. 
+ Não há chave primária composta (uma chave primária contendo mais de uma coluna) na mesa.  

#### <a name="usage"></a>Utilização

Para utilizar esta política, crie ou atualize a sua fonte de dados desta forma:

    {
        "name" : "myazuresqldatasource",
        "type" : "azuresql",
        "credentials" : { "connectionString" : "connection string" },
        "container" : { "name" : "table or view name" },
        "dataChangeDetectionPolicy" : {
           "@odata.type" : "#Microsoft.Azure.Search.SqlIntegratedChangeTrackingPolicy"
      }
    }

Ao utilizar a política integrada de rastreio de alterações sqL, não especifique uma política separada de deteção de eliminação de dados - esta política tem suporte incorporado para identificar linhas eliminadas. No entanto, para que as eliminações sejam detetadas "automaticamente", a chave de documentos no seu índice de pesquisa deve ser a mesma que a chave primária na tabela SQL. 

> [!NOTE]  
> Ao utilizar a [TABELA TRUNCATE](https://docs.microsoft.com/sql/t-sql/statements/truncate-table-transact-sql) para remover um grande número de linhas de uma tabela SQL, o indexante precisa de ser [reiniciado](https://docs.microsoft.com/rest/api/searchservice/reset-indexer) para redefinir o estado de rastreio de mudança para recolher as supressões da linha.

<a name="HighWaterMarkPolicy"></a>

### <a name="high-water-mark-change-detection-policy"></a>Política de deteção de alterações de marca de alta água

Esta política de deteção de alterações baseia-se numa coluna de "alta marca de água" que captura a versão ou o tempo em que uma linha foi atualizada pela última vez. Se está a usar uma vista, tem de usar uma política de marca de água elevada. A coluna de marca de água alta deve satisfazer os seguintes requisitos.

#### <a name="requirements"></a>Requisitos 

* Todas as inserções especificam um valor para a coluna.
* Todas as atualizações de um item também alteram o valor da coluna.
* O valor desta coluna aumenta a cada inserção ou atualização.
* As consultas com as seguintes cláusulas WHERE e ORDER BY podem ser executadas de forma eficiente:`WHERE [High Water Mark Column] > [Current High Water Mark Value] ORDER BY [High Water Mark Column]`

> [!IMPORTANT] 
> Recomendamos vivamente a utilização do tipo de dados de [versão de linha](https://docs.microsoft.com/sql/t-sql/data-types/rowversion-transact-sql) para a coluna de marca de água elevada. Se qualquer outro tipo de dados for utilizado, o rastreio de alterações não é garantido para capturar todas as alterações na presença de transações que executam simultaneamente com uma consulta indexante. Ao utilizar a versão de **linha** numa configuração com réplicas apenas para leitura, deve apontar o indexante para a réplica primária. Apenas uma réplica primária pode ser usada para cenários de sincronização de dados.

#### <a name="usage"></a>Utilização

Para utilizar uma política de marca de alta água, crie ou atualize a sua fonte de dados como esta:

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
> Se a tabela de origem não tiver um índice na coluna de marca de água elevada, as consultas utilizadas pelo indexante SQL podem esgotar-se. Em particular, `ORDER BY [High Water Mark Column]` a cláusula requer que um índice seja executado de forma eficiente quando a tabela contém muitas linhas.
>
>

Se encontrar erros de tempo limite, pode utilizar a configuração do `queryTimeout` indexante para definir o tempo limite de consulta para um valor superior ao intervalo de 5 minutos predefinido. Por exemplo, para definir o tempo de paragem para 10 minutos, criar ou atualizar o indexante com a seguinte configuração:

    {
      ... other indexer definition properties
     "parameters" : {
            "configuration" : { "queryTimeout" : "00:10:00" } }
    }

Também pode desativar a `ORDER BY [High Water Mark Column]` cláusula. No entanto, isto não é recomendado porque se a execução do indexante for interrompida por um erro, o indexante tem de reprocessar todas as linhas se for mais tarde - mesmo que o indexante já tenha processado quase todas as linhas até à sua interparação. Para desativar `ORDER BY` a `disableOrderByHighWaterMarkColumn` cláusula, utilize a definição na definição do indexador:  

    {
     ... other indexer definition properties
     "parameters" : {
            "configuration" : { "disableOrderByHighWaterMarkColumn" : true } }
    }

### <a name="soft-delete-column-deletion-detection-policy"></a>Política de deteção de eliminação de colunas soft delete
Quando as linhas são apagadas da tabela de origem, provavelmente também pretende eliminar essas linhas do índice de pesquisa. Se utilizar a política integrada de rastreio de mudanças sql, esta é cuidada por si. No entanto, a política de rastreio de marcas de água alta não o ajuda com linhas apagadas. O que fazer?

Se as linhas forem fisicamente removidas da mesa, a Pesquisa Cognitiva Azure não tem como inferir a presença de registos que já não existem.  No entanto, pode utilizar a técnica de "soft-delete" para eliminar logicamente as linhas sem as retirar da mesa. Adicione uma coluna à sua mesa ou vista e marque linhas como eliminadas utilizando essa coluna.

Ao utilizar a técnica de eliminação suave, pode especificar a política de eliminação suave da seguinte forma ao criar ou atualizar a fonte de dados:

    {
        …,
        "dataDeletionDetectionPolicy" : {
           "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
           "softDeleteColumnName" : "[a column name]",
           "softDeleteMarkerValue" : "[the value that indicates that a row is deleted]"
        }
    }

O **softDeleteMarkerValue** deve ser uma corda – utilize a representação de cordas do seu valor real. Por exemplo, se tiver uma coluna inteiro onde as linhas apagadas `"1"`são marcadas com o valor 1, utilize . Se tiver uma coluna BIT onde as linhas apagadas são marcadas `True` `true`com o verdadeiro valor booleano, use a corda literal ou , o caso não importa.

<a name="TypeMapping"></a>

## <a name="mapping-between-sql-and-azure-cognitive-search-data-types"></a>Mapeamento entre tipos de dados de pesquisa cognitiva SQL e Azure
| Tipo de dados SQL | Tipos de campo de índice de destino permitidos | Notas |
| --- | --- | --- |
| bit |Edm.Boolean, Edm.String | |
| int, menor, minúsculo |Edm.Int32, Edm.Int64, Edm.String | |
| bigint |Edm.Int64, Edm.String | |
| real, flutuante |Edm.Double, Edm.String | |
| dinheiro pequeno, dinheiro decimal numérico |Edm.String |A Pesquisa Cognitiva Azure não suporta a conversão de tipos decimais em Edm.Double porque isso perderia precisão |
| char, nchar, varchar, nvarchar |Edm.String<br/>Coleção (Edm.String) |Uma cadeia SQL pode ser usada para povoar um campo de coleção (Edm.String) se a cadeia representar um conjunto de cordas JSON:`["red", "white", "blue"]` |
| data de data, data, data2, data, data, dataoffset |Edm.DateTimeOffset, Edm.String | |
| únicoidentificador |Edm.String | |
| geografia |Edm.GeographyPoint |Apenas são suportados casos de geografia de tipo POINT com SRID 4326 (que é o padrão) |
| linhaversão |N/D |As colunas de versão de linha não podem ser armazenadas no índice de pesquisa, mas podem ser usadas para o rastreio de alterações |
| tempo, tempo, binário, varbinary, imagem, xml, geometria, tipos CLR |N/D |Não suportado |

## <a name="configuration-settings"></a>Definições de configuração
O indexante SQL expõe várias definições de configuração:

| Definição | Tipo de dados | Objetivo | Valor predefinido |
| --- | --- | --- | --- |
| consultaTimeout |string |Define o prazo para a execução de consulta SQL |5 minutos ("00:05:00") |
| desativarOrderByHighWaterMarkColumn |bool |Faz com que a consulta SQL utilizada pela política de marca de alta água omita a cláusula ORDER BY. Ver [política de High Water Mark](#HighWaterMarkPolicy) |false |

Estas definições são `parameters.configuration` utilizadas no objeto na definição do indexante. Por exemplo, para definir o tempo de consulta para 10 minutos, criar ou atualizar o indexante com a seguinte configuração:

    {
      ... other indexer definition properties
     "parameters" : {
            "configuration" : { "queryTimeout" : "00:10:00" } }
    }

## <a name="faq"></a>FAQ

**P: Posso usar o indexador Azure SQL com bases de dados SQL em execução em VMs IaaS em Azure?**

Sim. No entanto, é necessário que o seu serviço de pesquisa se conectem à sua base de dados. Para mais informações, consulte [Configure uma ligação de um indexador de pesquisa cognitiva Azure ao SQL Server num VM Azure](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md).

**P: Posso usar o indexador Azure SQL com bases de dados SQL em funcionamento no local?**

Não diretamente. Não recomendamos nem apoiamos uma ligação direta, pois isso exigiria que abrisse as suas bases de dados para o tráfego da Internet. Os clientes conseguiram com este cenário usando tecnologias de ponte como a Azure Data Factory. Para mais informações, consulte os dados do Push para um índice de [pesquisa cognitiva Azure utilizando](https://docs.microsoft.com/azure/data-factory/data-factory-azure-search-connector)a Azure Data Factory .

**P: Posso usar o indexador Azure SQL com bases de dados que não o SQL Server em execução no IaaS em Azure?**

Não. Não apoiamos este cenário, porque não testámos o indexante com bases de dados que não seja o SQL Server.  

**P: Posso criar vários indexantes em funcionamento num horário?**

Sim. No entanto, apenas um indexante pode estar a correr com um nó de uma vez. Se precisar de vários indexantes em funcionamento simultâneo, considere aumentar o seu serviço de pesquisa para mais do que uma unidade de pesquisa.

**P: Executar um indexador afeta a minha carga de trabalho de consulta?**

Sim. O Indexer funciona com um dos nós do seu serviço de pesquisa, e que os recursos do nó são partilhados entre indexação e serviço de tráfego de consulta e outros pedidos de API. Se executar cargas de trabalho intensivas de indexação e consulta e encontrar uma taxa elevada de 503 erros ou aumentar os tempos de resposta, considere [aumentar o seu serviço](search-capacity-planning.md)de pesquisa .

**P: Posso usar uma réplica secundária num cluster de [falhas](https://docs.microsoft.com/azure/sql-database/sql-database-geo-replication-overview) como fonte de dados?**

Depende. Para uma indexação completa de uma tabela ou vista, pode utilizar uma réplica secundária. 

Para indexação incremental, a Pesquisa Cognitiva Azure suporta duas políticas de deteção de alterações: rastreio integrado de alterações SQL e Marca de Alta Água.

Em réplicas apenas de leitura, a base de dados SQL não suporta o rastreio integrado de alterações. Portanto, deve usar a política de High Water Mark. 

A nossa recomendação padrão é usar o tipo de dados de versão de linha para a coluna de marca de água elevada. No entanto, a utilização da versão `MIN_ACTIVE_ROWVERSION` de linha baseia-se na função da Base de Dados SQL, que não é suportada em réplicas apenas de leitura. Portanto, deve apontar o indexador para uma réplica primária se estiver a usar a versão de linha.

Se tentar utilizar a versão de linha numa réplica apenas para leitura, verá o seguinte erro: 

    "Using a rowversion column for change tracking is not supported on secondary (read-only) availability replicas. Please update the datasource and specify a connection to the primary availability replica.Current database 'Updateability' property is 'READ_ONLY'".

**P: Posso usar uma coluna alternativa de não-rowversion para o rastreio de mudanças de marca de água alta?**

Não é recomendado. Só a **versão de linha** permite uma sincronização fiável de dados. No entanto, dependendo da sua lógica de candidatura, pode ser seguro se:

+ Pode garantir que, quando o indexante funciona, não existem transações pendentes na tabela que estão a ser indexadas (por exemplo, todas as atualizações de tabelas acontecem como um lote em um horário, e o calendário do indexante de pesquisa cognitiva Azure está definido para evitar sobrepor-se com a tabela calendário de atualização).  

+ Faz periodicamente um reíndice completo para apanhar as linhas perdidas. 