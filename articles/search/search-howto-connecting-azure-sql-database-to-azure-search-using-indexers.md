---
title: Pesquisa sobre os dados do Azure SQL
titleSuffix: Azure Cognitive Search
description: Importar dados da Base de Dados Azure SQL ou sql Gestd Instance usando indexantes, para pesquisa completa de texto na Pesquisa Cognitiva de Azure. Este artigo abrange ligações, configuração do indexante e ingestão de dados.
manager: nitinme
author: mgottein
ms.author: magottei
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 07/12/2020
ms.openlocfilehash: b1ad4ead83c9e07966f921a5b192f2791838e6ef
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91530566"
---
# <a name="connect-to-and-index-azure-sql-content-using-an-azure-cognitive-search-indexer"></a>Ligue e indexe o conteúdo Azure SQL usando um indexador de pesquisa cognitiva Azure

Antes de consultar um [índice de Pesquisa Cognitiva Azure,](search-what-is-an-index.md)deve povoá-lo com os seus dados. Se os dados viverem na Base de Dados Azure SQL ou na SQL Managed Instance, um **indexante de pesquisa cognitiva Azure para a Base de Dados Azure SQL** (ou **indexante Azure SQL** para abreviar) pode automatizar o processo de indexação, o que significa menos código para escrever e menos infraestruturas para se preocupar.

Este artigo cobre a mecânica da utilização de [indexadores,](search-indexer-overview.md)mas também descreve funcionalidades disponíveis apenas com base de dados Azure SQL ou SQL Managed Instance (por exemplo, rastreio de alterações integrado). 

Além da Base de Dados Azure SQL e da SQL Managed Instance, a Azure Cognitive Search fornece indexadores para [Azure Cosmos DB,](search-howto-index-cosmosdb.md) [armazenamento de Azure Blob](search-howto-indexing-azure-blob-storage.md)e [armazenamento de mesa Azure](search-howto-indexing-azure-tables.md). Para solicitar suporte para outras fontes de dados, forneça o seu feedback sobre o [fórum de feedback da Azure Cognitive Search](https://feedback.azure.com/forums/263029-azure-search/).

## <a name="indexers-and-data-sources"></a>Indexantes e fontes de dados

Uma **fonte de dados** especifica quais os dados a indexar, credenciais para acesso a dados e políticas que identificam de forma eficiente as alterações nos dados (linhas novas, modificadas ou eliminadas). É definido como um recurso independente para que possa ser usado por vários indexadores.

Um **indexante** é um recurso que liga uma única fonte de dados com um índice de pesquisa direcionado. Um indexante é utilizado das seguintes formas:

* Execute uma cópia única dos dados para preencher um índice.
* Atualizar um índice com alterações na fonte de dados num horário.
* Corra a pedido para atualizar um índice conforme necessário.

Um indexador único só pode consumir uma tabela ou uma vista, mas pode criar vários indexadores se quiser preencher vários índices de pesquisa. Para obter mais informações sobre conceitos, consulte [Indexer Operations: Típico fluxo de trabalho.](/rest/api/searchservice/Indexer-operations#typical-workflow)

Pode configurar e configurar um indexador Azure SQL utilizando:

* Assistente de dados de importação no [portal Azure](https://portal.azure.com)
* Pesquisa Cognitiva Azure [.NET SDK](/dotnet/api/microsoft.azure.search.models.indexer)
* Azure Cognitive Search [REST API](/rest/api/searchservice/indexer-operations)

Neste artigo, usaremos a API REST para criar **indexantes** e **fontes de dados.**

## <a name="when-to-use-azure-sql-indexer"></a>Quando utilizar o Indexador Azure SQL
Dependendo de vários fatores relacionados com os seus dados, a utilização do indexante Azure SQL pode ou não ser apropriado. Se os seus dados se adequarem aos seguintes requisitos, pode utilizar o indexador Azure SQL.

| Critérios | Detalhes |
|----------|---------|
| Os dados são originários de uma única tabela ou vista | Se os dados estiverem espalhados por várias tabelas, pode criar uma única visão dos dados. No entanto, se utilizar uma vista, não poderá utilizar a deteção de alterações integradas do SQL Server para atualizar um índice com alterações incrementais. Para obter mais informações, consulte [Capturing Changed and Deleted Rows](#CaptureChangedRows) abaixo. |
| Os tipos de dados são compatíveis | A maioria, mas nem todos os tipos DE SQL são suportados num índice de Pesquisa Cognitiva Azure. Para obter uma lista, consulte [os tipos de dados de mapeamento](#TypeMapping). |
| Não é necessária sincronização de dados em tempo real | Um indexante pode reindexar a sua mesa no máximo a cada cinco minutos. Se os seus dados forem frequentemente alterados e as alterações precisarem de ser refletidas no índice dentro de segundos ou minutos, recomendamos a utilização da [API REST](/rest/api/searchservice/AddUpdate-or-Delete-Documents) ou [da .NET SDK](./search-get-started-dotnet.md) para empurrar diretamente as linhas atualizadas. |
| A indexação incremental é possível | Se tiver um grande conjunto de dados e planeia executar o indexante num horário, o Azure Cognitive Search deve ser capaz de identificar de forma eficiente novas, alteradas ou eliminadas linhas. A indexação não incremental só é permitida se estiver a indexar a pedido (não no horário), ou a indexar menos de 100.000 linhas. Para obter mais informações, consulte [Capturing Changed and Deleted Rows](#CaptureChangedRows) abaixo. |

> [!NOTE] 
> A Azure Cognitive Search suporta apenas a autenticação do SQL Server. Se necessitar de suporte para autenticação de password do Diretório Ativo Azure, por favor vote nesta [sugestão UserVoice](https://feedback.azure.com/forums/263029-azure-search/suggestions/33595465-support-azure-active-directory-password-authentica).

## <a name="create-an-azure-sql-indexer"></a>Criar um Indexador SQL Azure

1. Criar a fonte de dados:

   ```
    POST https://myservice.search.windows.net/datasources?api-version=2020-06-30
    Content-Type: application/json
    api-key: admin-key

    {
        "name" : "myazuresqldatasource",
        "type" : "azuresql",
        "credentials" : { "connectionString" : "Server=tcp:<your server>.database.windows.net,1433;Database=<your database>;User ID=<your user name>;Password=<your password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30;" },
        "container" : { "name" : "name of the table or view that you want to index" }
    }
   ```

   A cadeia de ligação pode seguir qualquer um dos formatos abaixo:
    1. Pode obter a cadeia de ligação do [portal Azure;](https://portal.azure.com) usar a `ADO.NET connection string` opção.
    1. Uma cadeia de ligação de identidade gerida que não inclui uma chave de conta com o seguinte formato: `Initial Catalog|Database=<your database name>;ResourceId=/subscriptions/<your subscription ID>/resourceGroups/<your resource group name>/providers/Microsoft.Sql/servers/<your SQL Server name>/;Connection Timeout=connection timeout length;` . Para utilizar esta cadeia de ligação, siga as instruções para [configurar uma ligação indexante a uma Base de Dados Azure SQL utilizando uma identidade gerida](search-howto-managed-identities-sql.md).

2. Crie o índice de pesquisa cognitiva Azure alvo se ainda não tiver um. Pode criar um índice utilizando o [portal](https://portal.azure.com) ou a [API do Índice de Criação](/rest/api/searchservice/Create-Index). Certifique-se de que o esquema do seu índice-alvo é compatível com o esquema da tabela de origem - consulte o mapeamento entre os tipos de [dados de pesquisa cognitiva SQL e Azure .](#TypeMapping)

3. Crie o indexante dando-lhe um nome e referindo a fonte de dados e o índice alvo:

   ```
    POST https://myservice.search.windows.net/indexers?api-version=2020-06-30
    Content-Type: application/json
    api-key: admin-key

    {
        "name" : "myindexer",
        "dataSourceName" : "myazuresqldatasource",
        "targetIndexName" : "target index name"
    }
   ```

Um indexante criado desta forma não tem um horário. Funciona automaticamente uma vez quando é criado. Pode executá-lo novamente a qualquer momento usando um pedido **de indexante executado:**

```
    POST https://myservice.search.windows.net/indexers/myindexer/run?api-version=2020-06-30
    api-key: admin-key
```

Pode personalizar vários aspetos do comportamento do indexante, tais como o tamanho do lote e quantos documentos podem ser ignorados antes que uma execução indexante falhe. Para obter mais informações, consulte [Create Indexer API](/rest/api/searchservice/Create-Indexer).

Poderá ter de permitir que os serviços da Azure se conectem à sua base de dados. Consulte [a Ligação de Azure](../azure-sql/database/firewall-configure.md) para obter instruções sobre como fazê-lo.

Para monitorizar o estado do indexante e o histórico de execução (número de itens indexados, falhas, etc.), utilize um pedido **de estado indexante:**

```
    GET https://myservice.search.windows.net/indexers/myindexer/status?api-version=2020-06-30
    api-key: admin-key
```

A resposta deve ser semelhante à seguinte:

```
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
```

O histórico de execução contém até 50 das execuções mais recentes concluídas, que são ordenadas na ordem cronológica inversa (de modo que a última execução vem em primeiro lugar na resposta).
Informações adicionais sobre a resposta podem ser encontradas no [Estado do Indexante](/rest/api/searchservice/get-indexer-status)

## <a name="run-indexers-on-a-schedule"></a>Executar indexadores em um horário
Também pode organizar o indexante para funcionar periodicamente num horário. Para isso, adicione a propriedade do **horário** ao criar ou atualizar o indexante. O exemplo abaixo mostra um pedido DEP para atualizar o indexante:

```
    PUT https://myservice.search.windows.net/indexers/myindexer?api-version=2020-06-30
    Content-Type: application/json
    api-key: admin-key

    {
        "dataSourceName" : "myazuresqldatasource",
        "targetIndexName" : "target index name",
        "schedule" : { "interval" : "PT10M", "startTime" : "2015-01-01T00:00:00Z" }
    }
```

O parâmetro **de intervalo** é necessário. O intervalo refere-se ao tempo entre o início de duas execuções indexantes consecutivas. O menor intervalo permitido é de 5 minutos; o mais longo é um dia. Deve ser formatado como um valor XSD "dayTimeDuration" (um subconjunto restrito de um valor de [duração ISO 8601).](https://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) O padrão para isto é: `P(nD)(T(nH)(nM))` . Exemplos: `PT15M` por cada 15 minutos, `PT2H` por cada 2 horas.

Para obter mais informações sobre a definição de horários de indexantes, consulte [Como agendar indexadores para a Pesquisa Cognitiva do Azure](search-howto-schedule-indexers.md).

<a name="CaptureChangedRows"></a>

## <a name="capture-new-changed-and-deleted-rows"></a>Capturar novas linhas alteradas, alteradas e eliminadas

A Azure Cognitive Search usa **indexação incremental** para evitar ter que reindexar toda a tabela ou visualizar cada vez que um indexante corre. A Azure Cognitive Search fornece duas políticas de deteção de alterações para suportar a indexação incremental. 

### <a name="sql-integrated-change-tracking-policy"></a>Política integrada de rastreio de alterações DA SQL
Se a sua base de dados SQL suportar o rastreio de [alterações,](/sql/relational-databases/track-changes/about-change-tracking-sql-server)recomendamos a utilização **da Política integrada de rastreio de alterações SQL.** Esta é a política mais eficiente. Além disso, permite que a Azure Cognitive Search identifique linhas eliminadas sem ter de adicionar uma coluna explícita de "soft delete" à sua mesa.

#### <a name="requirements"></a>Requisitos 

+ Requisitos de versão da base de dados:
  * SQL Server 2012 SP3 e mais tarde, se estiver a utilizar o SQL Server em VMs Azure.
  * Azure SQL Database ou SQL Managed Instance.
+ Tabelas apenas (sem vistas). 
+ Na base de dados, ative o [rastreio de alterações](/sql/relational-databases/track-changes/enable-and-disable-change-tracking-sql-server) para a tabela. 
+ Não há chave primária composta (uma chave primária que contenha mais de uma coluna) sobre a tabela.  

#### <a name="usage"></a>Utilização

Para utilizar esta política, crie ou atualize a sua fonte de dados desta forma:

```
    {
        "name" : "myazuresqldatasource",
        "type" : "azuresql",
        "credentials" : { "connectionString" : "connection string" },
        "container" : { "name" : "table or view name" },
        "dataChangeDetectionPolicy" : {
           "@odata.type" : "#Microsoft.Azure.Search.SqlIntegratedChangeTrackingPolicy"
      }
    }
```

Ao utilizar a política integrada de rastreio de alterações SQL, não especifique uma política separada de deteção de eliminação de dados - esta política tem suporte incorporado para identificar linhas eliminadas. No entanto, para que as eliminações sejam detetadas "automagicamente", a chave de documento no seu índice de pesquisa deve ser a mesma que a chave primária na tabela SQL. 

> [!NOTE]  
> Ao utilizar a [TABELA TRUNCATE](/sql/t-sql/statements/truncate-table-transact-sql) para remover um grande número de linhas de uma tabela SQL, o indexante precisa de ser [reiniciado](/rest/api/searchservice/reset-indexer) para redefinir o estado de rastreio de alterações para recolher as supressões de linha.

<a name="HighWaterMarkPolicy"></a>

### <a name="high-water-mark-change-detection-policy"></a>Política de deteção de alterações de marca de alta água

Esta política de deteção de alterações baseia-se numa coluna de "marca de água elevada" que captura a versão ou hora em que uma linha foi atualizada pela última vez. Se estiver a usar uma vista, deve usar uma política de marca de água elevada. A coluna de alta marca de água deve satisfazer os seguintes requisitos.

#### <a name="requirements"></a>Requisitos 

* Todos os inserções especificam um valor para a coluna.
* Todas as atualizações para um item também alteram o valor da coluna.
* O valor desta coluna aumenta com cada inserção ou atualização.
* As consultas com as seguintes cláusulas WHERE e ORDER BY podem ser executadas de forma eficiente: `WHERE [High Water Mark Column] > [Current High Water Mark Value] ORDER BY [High Water Mark Column]`

> [!IMPORTANT] 
> Recomendamos vivamente a utilização do tipo [de dados de remar](/sql/t-sql/data-types/rowversion-transact-sql) para a coluna de alta marca de água. Se qualquer outro tipo de dados for utilizado, o rastreio de alterações não é garantido para capturar todas as alterações na presença de transações executadas simultaneamente com uma consulta indexante. Ao utilizar **a versão de linha** numa configuração com réplicas apenas de leitura, deve apontar o indexante para a réplica primária. Apenas uma réplica primária pode ser usada para cenários de sincronização de dados.

#### <a name="usage"></a>Utilização

Para utilizar uma política de marca de água elevada, crie ou atualize a sua fonte de dados desta forma:

```
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
```

> [!WARNING]
> Se a tabela de origem não tiver um índice na coluna de alta marca de água, as consultas utilizadas pelo indexante SQL podem esgotar-se. Em particular, a `ORDER BY [High Water Mark Column]` cláusula requer que um índice seja executado de forma eficiente quando a tabela contém muitas linhas.
>
>

<a name="convertHighWaterMarkToRowVersion"></a>

##### <a name="converthighwatermarktorowversion"></a>converterHighWaterMarkToRowVersion

Se estiver a utilizar um tipo [de dados de partilha de linha](/sql/t-sql/data-types/rowversion-transact-sql) para a coluna de alta marca de água, considere utilizar a definição de configuração do `convertHighWaterMarkToRowVersion` indexante. `convertHighWaterMarkToRowVersion` faz duas coisas:

* Utilize o tipo de dados de partilha de linha para a coluna de alta marca de água na consulta sql indexante. A utilização do tipo de dados correto melhora o desempenho da consulta do indexante.
* Subtrair 1 do valor de remar antes da consulta do indexante. Vistas com 1 a muitos junções podem ter linhas com valores duplicados de remar. Subtrair 1 garante que a consulta do indexante não perca estas linhas.

Para ativar esta funcionalidade, crie ou atualize o indexante com a seguinte configuração:

```
    {
      ... other indexer definition properties
     "parameters" : {
            "configuration" : { "convertHighWaterMarkToRowVersion" : true } }
    }
```

<a name="queryTimeout"></a>

##### <a name="querytimeout"></a>consultaTimeout

Se encontrar erros de tempo limite, pode utilizar a definição de configuração do `queryTimeout` indexante para definir o tempo limite de consulta para um valor superior ao tempo limite de 5 minutos predefinido. Por exemplo, para definir o tempo limite para 10 minutos, criar ou atualizar o indexante com a seguinte configuração:

```
    {
      ... other indexer definition properties
     "parameters" : {
            "configuration" : { "queryTimeout" : "00:10:00" } }
    }
```

<a name="disableOrderByHighWaterMarkColumn"></a>

##### <a name="disableorderbyhighwatermarkcolumn"></a>desativarByHighWaterMarkColumn

Também pode desativar a `ORDER BY [High Water Mark Column]` cláusula. No entanto, isto não é recomendado porque se a execução do indexante for interrompida por um erro, o indexante tem de reencampor todas as linhas se for executado mais tarde - mesmo que o indexante já tenha processado quase todas as linhas até ao momento em que foi interrompido. Para desativar a `ORDER BY` cláusula, utilize a `disableOrderByHighWaterMarkColumn` definição na definição do indexante:  

```
    {
     ... other indexer definition properties
     "parameters" : {
            "configuration" : { "disableOrderByHighWaterMarkColumn" : true } }
    }
```

### <a name="soft-delete-column-deletion-detection-policy"></a>Política de deteção de eliminação de colunas soft delete
Quando as linhas são eliminadas da tabela de origem, provavelmente também pretende eliminar essas linhas do índice de pesquisa. Se utilizar a política de rastreio de alterações integrada SQL, esta é cuidada por si. No entanto, a política de rastreio de mudanças de marca de água alta não o ajuda com linhas apagadas. O que fazer?

Se as linhas forem fisicamente removidas da mesa, a Azure Cognitive Search não tem como inferir a presença de registos que já não existem.  No entanto, pode utilizar a técnica "soft-delete" para eliminar logicamente as linhas sem as retirar da mesa. Adicione uma coluna à sua mesa ou vista e marque linhas como eliminadas usando esta coluna.

Ao utilizar a técnica de eliminação suave, pode especificar a política de eliminação suave da seguinte forma ao criar ou atualizar a fonte de dados:

```
    {
        …,
        "dataDeletionDetectionPolicy" : {
           "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
           "softDeleteColumnName" : "[a column name]",
           "softDeleteMarkerValue" : "[the value that indicates that a row is deleted]"
        }
    }
```

O **softDeleteMarkerValue** deve ser uma corda – use a representação de cordas do seu valor real. Por exemplo, se tiver uma coluna de inteiros onde as linhas apagadas são marcadas com o valor 1, utilize `"1"` . Se tiver uma coluna BIT onde as linhas apagadas são marcadas com o valor verdadeiro booleano, use a corda literal `True` ou , a caixa não `true` importa.

<a name="TypeMapping"></a>

## <a name="mapping-between-sql-and-azure-cognitive-search-data-types"></a>Mapeamento entre os tipos de dados de pesquisa cognitiva SQL e Azure
| Tipo de dados SQL | Tipos de campo de índice de alvo permitidos | Notas |
| --- | --- | --- |
| bit |Edm.Boolean, Edm.String | |
| int, smallint, minúsculo |Edm.Int32, Edm.Int64, Edm.String | |
| bigint |Edm.Int64, Edm.String | |
| real, flutuar |Edm.Double, Edm.String | |
| pequeno dinheiro, dinheiro decimal numérico |Edm.String |A Azure Cognitive Search não suporta a conversão de tipos decimais em Edm.Double porque isso perderia precisão |
| char, nchar, varchar, nvarchar |Edm.String<br/>Coleção (Edm.String) |Uma corda SQL pode ser usada para povoar um campo Collection (Edm.String) se a corda representar uma matriz de cordas JSON: `["red", "white", "blue"]` |
| hora pequena, data, data 2, data, data, data |Edm.DateTimeOffset, Edm.String | |
| único identificador |Edm.String | |
| geografia |Edm.GeographyPoint |Apenas são suportados casos de geografia do tipo POINT com SRID 4326 (que é o padrão) |
| rowversão |N/D |As colunas de versão de linha não podem ser armazenadas no índice de pesquisa, mas podem ser usadas para rastrear alterações |
| tempo, tempo, binário, varbinário, imagem, xml, geometria, tipos de CLR |N/D |Não suportado |

## <a name="configuration-settings"></a>Definições de configuração
Indexante SQL expõe várias definições de configuração:

| Definição | Tipo de dados | Objetivo | Valor predefinido |
| --- | --- | --- | --- |
| consultaTimeout |string |Define o tempo limite para a execução de consultas SQL |5 minutos ("00:05:00") |
| desativarByHighWaterMarkColumn |bool |Causa a consulta SQL utilizada pela política de marca de água elevada para omitir a cláusula ORDER BY. Ver [política de marca de alta água](#HighWaterMarkPolicy) |false |

Estas definições são utilizadas no `parameters.configuration` objeto na definição do indexante. Por exemplo, para definir o tempo limite de consulta para 10 minutos, criar ou atualizar o indexante com a seguinte configuração:

```
    {
      ... other indexer definition properties
     "parameters" : {
            "configuration" : { "queryTimeout" : "00:10:00" } }
    }
```

## <a name="faq"></a>FAQ

**P: Posso utilizar o indexador Azure SQL com bases de dados SQL em funcionamento em IaaS VMs em Azure?**

Sim. No entanto, tem de permitir que o seu serviço de pesquisa se conecte à sua base de dados. Para obter mais informações, consulte [configurar uma ligação de um indexante de Pesquisa Cognitiva Azure ao SQL Server num VM Azure](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md).

**P: Posso utilizar o indexador Azure SQL com bases de dados SQL a funcionar no local?**

Não diretamente. Não recomendamos nem apoiamos uma ligação direta, pois isso exigiria que abrisse as suas bases de dados ao tráfego da Internet. Os clientes conseguiram com este cenário utilizar tecnologias de ponte como a Azure Data Factory. Para obter mais informações, consulte [os dados push para um índice de pesquisa cognitiva Azure usando Azure Data Factory](../data-factory/v1/data-factory-azure-search-connector.md).

**P: Posso utilizar o indexador Azure SQL com bases de dados que não o SQL Server em funcionamento em IaaS em Azure?**

N.º Não apoiamos este cenário, porque não testámos o indexante com bases de dados que não o SQL Server.  

**P: Posso criar vários indexadores em execução num horário?**

Sim. No entanto, apenas um indexante pode estar a funcionar com um nó de cada vez. Se precisar de vários indexadores em execução em simultâneo, considere aumentar o seu serviço de pesquisa para mais de uma unidade de pesquisa.

**P: Executar um indexante afeta a minha carga de trabalho de consulta?**

Sim. O Indexer funciona num dos nós do seu serviço de pesquisa, e os recursos do nó são partilhados entre indexação e serviço de tráfego de consulta e outros pedidos de API. Se executar cargas de trabalho de indexação e consulta intensivas e encontrar uma taxa elevada de 503 erros ou aumentar os tempos de resposta, considere [aumentar o seu serviço de pesquisa](search-capacity-planning.md).

**P: Posso usar uma réplica secundária num [cluster de falha](../azure-sql/database/auto-failover-group-overview.md) como fonte de dados?**

Depende. Para indexação completa de uma tabela ou vista, pode utilizar uma réplica secundária. 

Para indexação incremental, a Azure Cognitive Search suporta duas políticas de deteção de alterações: rastreio integrado de alterações SQL e Marca de Alta Água.

Nas réplicas apenas de leitura, a BASE de Dados SQL não suporta o rastreio integrado de alterações. Portanto, deve usar a política da Marca de Alta Água. 

A nossa recomendação padrão é usar o tipo de dados de partilha de linha para a coluna de alta marca de água. No entanto, a utilização da versão de linha `MIN_ACTIVE_ROWVERSION` baseia-se na função, que não é suportada em réplicas apenas de leitura. Portanto, deve apontar o indexante para uma réplica primária se estiver a utilizar a versão de linha.

Se tentar utilizar a versão de linha numa réplica apenas de leitura, verá o seguinte erro: 

"A utilização de uma coluna de remar para o rastreio de alterações não é suportada em réplicas de disponibilidade secundária (apenas de leitura). Por favor, atualize a fonte de dados e especifique uma ligação à réplica de disponibilidade primária. A propriedade 'Updateability' da base de dados atual é 'READ_ONLY'".

**P: Posso usar uma coluna alternativa de não-rowversão para rastreamento de alta marca de água?**

Não é recomendado. Apenas **a partilha de linha** permite uma sincronização fiável de dados. No entanto, dependendo da lógica da sua aplicação, pode ser seguro se:

+ Pode garantir que, quando o indexante funciona, não há transações pendentes na tabela que estão a ser indexadas (por exemplo, todas as atualizações de tabelas acontecem como um lote num horário, e o calendário do indexante de pesquisa cognitiva Azure está definido para evitar sobreposição com o calendário de atualização da tabela).  

+ Faz-se periodicamente uma reindexo completa para apanhar as filas perdidas.