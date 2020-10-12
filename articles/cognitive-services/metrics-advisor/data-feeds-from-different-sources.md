---
title: Como adicionar feeds de dados de diferentes fontes ao Metrics Advisor
titleSuffix: Azure Cognitive Services
description: adicionar diferentes feeds de dados ao Metrics Advisor
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 09/04/2020
ms.author: aahi
ms.openlocfilehash: 343db078880f55701730e096c3da85a6a7e5428a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91324472"
---
# <a name="add-data-feeds-from-different-data-sources-to-metrics-advisor"></a>Adicione feeds de dados de diferentes fontes de dados ao Metrics Advisor

Utilize este artigo para encontrar as configurações e requisitos para ligar diferentes tipos de fontes de dados ao Metrics Advisor. Certifique-se de ler como embarcar [os seus dados](how-tos/onboard-your-data.md) para saber sobre os conceitos-chave para a utilização dos seus dados com o Metrics Advisor. 

## <a name="supported-authentication-types"></a>Tipos de autenticação suportados

| Tipos de autenticação | Descrição |
| ---------------------|-------------|
|**Básica** | Terá de ser capaz de fornecer parâmetros básicos para aceder a fontes de dados. Por exemplo, uma cadeia de ligação ou uma chave. Os administradores de feed de dados são capazes de ver estas credenciais. |
| **AzureManagedIdentity** | [Identidades geridas](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) para recursos Azure é uma característica do Azure Ative Directory. Fornece aos serviços Azure uma identidade gerida automaticamente no Azure AD. Pode utilizar a identidade para autenticar qualquer serviço que suporte a autenticação AZure AD.|
| **AzuresQLConnectionString**| Guarde a sua cadeia de ligação AzureSQL como entidade **credencial** no Metrics Advisor e use-a diretamente sempre que estiver a bordo de dados de métricas. Apenas os administradores da entidade Credencial são capazes de ver estas credenciais, mas permite que os espectadores autorizados criem feeds de dados sem precisarem de saber detalhes para as credenciais. |
| **DataLakeGen2SharedKey**| Guarde a sua chave de conta de lago de dados como uma **entidade credencial** no Metrics Advisor e use-a diretamente de cada vez que estiver a bordo de dados de métricas. Apenas os administradores da entidade Credencial são capazes de ver estas credenciais, mas permite que os espectadores autorizados criem feed de dados sem precisarem de saber os detalhes credenciais.|
| **Diretor de serviços**| Guarde o seu principal de serviço como **entidade credencial** no Metrics Advisor e use-o diretamente sempre que estiver a bordo de dados de métricas. Apenas os administradores da entidade Credencial são capazes de ver as credenciais, mas permite que os espectadores autorizados criem feed de dados sem precisarem de saber os detalhes credenciais.|
| **Diretor de serviço do cofre chave**|Guarde o seu principal de serviço num cofre-chave como **entidade credencial** no Metrics Advisor e use-o diretamente sempre que estiver a bordo de dados de métricas. Apenas os administradores de uma **entidade credencial** são capazes de ver as credenciais, mas também deixam os espectadores capazes de criar feed de dados sem precisar de saber credenciais detalhadas. |

## <a name="data-sources-supported-and-corresponding-authentication-types"></a>Fontes de dados suportadas e correspondentes tipos de autenticação


| Origens de dados | Tipos de autenticação |
|-------------| ---------------------|
|[**Azure Application Insights**](#appinsights)|  Básico |
|[**Armazenamento Azure Blob (JSON)**](#blob) | Básico<br>ManagedIdentity|
|[**Azure Cosmos DB (SQL)**](#cosmosdb) | Básico |
|[**Azure Data Explorer (Kusto)**](#kusto) | Básico<br>ManagedIdentity|
|[**Armazenamento do Azure Data Lake Ger2**](#adl) | Básico<br>DataLakeGen2SharedKey<br>Service principal (Principal de serviço)<br>Diretor de serviço do cofre chave<br> |
|[**Base de Dados Azure SQL / Servidor SQL**](#sql) | Básico<br>ManagedIdentity<br>Service principal (Principal de serviço)<br>Diretor de serviço do cofre chave<br>AzuresQLConnectionString
|[**Armazenamento de mesa Azure**](#table) | Básico | 
|[**Elasticsearch**](#es) | Básico |
|[**Solicitar**](#http) | Básico | 
|[**InfluxDB (InfluxQL)**](#influxdb) | Básico |
|[**MongoDB**](#mongodb) | Básico |
|[**MySQL**](#mysql) | Básico |
|[**PostgreSQL**](#pgsql)| Básico|

Crie uma **entidade credencial** e utilize-a para autenticar as suas fontes de dados. As seguintes secções especificam os parâmetros exigidos para a autenticação *básica.* 

## <a name="span-idappinsightsazure-application-insightsspan"></a><span id="appinsights">Azure Application Insights</span>

* **ID de aplicação**: Este é usado para identificar esta aplicação ao utilizar a API de Insights de Aplicação. Para obter o ID da aplicação, faça o seguinte:

    1. A partir do seu recurso Application Insights, clique em Acesso API.

    2. Copie o ID de aplicação gerado no campo **de ID de aplicação** no Metrics Advisor. 
    
    Consulte a documentação do [Serviço Azure Bot](https://docs.microsoft.com/azure/bot-service/bot-service-resources-app-insights-keys#application-id) para obter mais informações.

* **Chave API**: As teclas API são utilizadas por aplicações fora do navegador para aceder a este recurso. Para obter a chave API, faça o seguinte:

    1. A partir do recurso Application Insights, clique em Acesso API.

    2. Clique em Criar Tecla API.

    3. Introduza uma breve descrição, verifique a opção de telemetria Ler e clique no botão 'Gerar' e clique no botão 'Gerar'.

    4. Copie a chave API para o campo **chave API** no Metrics Advisor.

* **Consulta**: Os registos Azure Application Insights são construídos no Azure Data Explorer e as consultas de registo do Azure Monitor utilizam uma versão da mesma língua de consulta Kusto. A documentação linguística de [consulta de Kusto](https://docs.microsoft.com/azure/data-explorer/kusto/query/) tem todos os detalhes para o idioma e deve ser o seu principal recurso para escrever uma consulta contra a Application Insights. 


## <a name="span-idblobazure-blob-storage-jsonspan"></a><span id="blob">Armazenamento Azure Blob (JSON)</span>

* **Cadeia de ligação**: Consulte o artigo [de cadeia de ligação](https://docs.microsoft.com/azure/storage/common/storage-configure-connection-string#view-and-copy-a-connection-string) Azure Blob Storage para obter informações sobre a recuperação desta cadeia.

* **Recipiente**: O Metrics Advisor espera que os dados da série de tempos armazenados como ficheiros Blob (um Blob por timetamp) num único recipiente. Este é o campo de nomes de contentores.

* **Modelo blob**: Este é o modelo dos nomes de ficheiros Blob. Por exemplo: `/%Y/%m/X_%Y-%m-%d-%h-%M.json`. São suportados os seguintes parâmetros:
  * `%Y` é o ano formatado como `yyyy`
  * `%m` é o mês formatado como `MM`
  * `%d` é o dia formatado como `dd`
  * `%h` é a hora formatada como `HH`
  * `%M` é o minuto formatado como `mm`

* **Versão em formato JSON**: Define o esquema de dados nos ficheiros JSON. Atualmente o Metrics Advisor suporta duas versões:
  
  * v1 (Valor Padrão)

      Apenas as métricas *Nome* e *Valor* são aceites. Por exemplo:
    
      ``` JSON
      {"count":11, "revenue":1.23}
      ```

  * v2

      As métricas *Dimensões* e *o tempotampício* também são aceites. Por exemplo:
      
      ``` JSON
      [
        {"date": "2018-01-01T00:00:00Z", "market":"en-us", "count":11, "revenue":1.23},
        {"date": "2018-01-01T00:00:00Z", "market":"zh-cn", "count":22, "revenue":4.56}
      ]
      ```

Apenas um calibre de tempo é permitido por ficheiro JSON. 

## <a name="span-idcosmosdbazure-cosmos-db-sqlspan"></a><span id="cosmosdb">Azure Cosmos DB (SQL)</span>

* **Cadeia de ligação**: O fio de ligação para aceder ao seu Azure Cosmos DB. Isto pode ser encontrado no recurso Cosmos DB, em **Keys.** 
* **Base de dados**: A base de dados para consulta. Isto pode ser encontrado na página **Browse** na secção **Contentores.**
* **ID de coleção**: O ID de coleção para consulta contra. Isto pode ser encontrado na página **Browse** na secção **Contentores.**
* **CONSULTA SQL**: Uma consulta SQL para obter e formular dados em dados multidimensionais de séries de tempo. Pode usar as `@StartTime` variáveis e `@EndTime` variáveis na sua consulta. Devem ser formatados: `yyyy-MM-dd HH:mm:ss` .

    Consulta de amostras:
    
    ``` mssql
    select StartDate, JobStatusId, COUNT(*) AS JobNumber from IngestionJobs WHERE and StartDate = @StartTime
    ```
    
    Consulta de amostra para uma fatia de dados de 2019/12/12:
    
    ``` mssql
    select StartDate, JobStatusId, COUNT(*) AS JobNumber from IngestionJobs WHERE and StartDate = '2019-12-12 00:00:00'
    ```

## <a name="span-idkustoazure-data-explorer-kustospan"></a><span id="kusto">Azure Data Explorer (Kusto)</span>

* **Cadeia de ligação**: Ver [e copiar uma cadeia de ligação](https://docs.microsoft.com/azure/data-explorer/kusto/api/connection-strings/kusto) para obter informações sobre como recuperar a cadeia de ligação do Azure Data Explorer (Kusto).

* **Consulta**: Consulte [a Linguagem de Consulta de Kusto](https://docs.microsoft.com/azure/data-explorer/kusto/query) para obter e formular dados em dados multidimensionais da série temporal. Pode usar as `@StartTime` variáveis e `@EndTime` variáveis na sua consulta. Devem ser formatados: `yyyy-MM-dd HH:mm:ss` .

## <a name="span-idadlazure-data-lake-storage-gen2span"></a><span id="adl">Azure Data Lake Storage Gen2</span> (Armazenamento do Azure Data Lake Gen2)

* **Nome da conta**: O nome da conta do seu Azure Data Lake Storage Gen2. Isto pode ser encontrado no seu recurso Azure Storage Account (Azure Data Lake Storage Gen2) nas **teclas Access**.

* **Tecla de conta**: Por favor, especifique o nome da conta para aceder ao seu Azure Data Lake Storage Gen2. Isto pode ser encontrado no recurso Azure Storage Account (Azure Data Lake Storage Gen2) na definição **das teclas de acesso.**

* **Nome do sistema de ficheiros (Recipiente)**: O Advisor de métricas espera que os dados da série de tempo sejam armazenados como ficheiros Blob (uma bolha por relógio) debaixo de um único recipiente. Este é o campo de nomes de contentores. Isto pode ser encontrado na sua conta de armazenamento Azure (Azure Data Lake Storage Gen2) e clique em 'Contentores' na secção 'Blob Service'.

* **Modelo de diretório**: Este é o modelo de diretório do ficheiro Blob. Por exemplo: */%Y/%m/%d*. São suportados os seguintes parâmetros:
  * `%Y` é o ano formatado como `yyyy`
  * `%m` é o mês formatado como `MM`
  * `%d` é o dia formatado como `dd`
  * `%h` é a hora formatada como `HH`
  * `%M` é o minuto formatado como `mm`

* **Modelo de ficheiro**: Este é o modelo de ficheiro do ficheiro Blob. Por exemplo: *X_%Y-%m-%d-%h-%M.jsem*. São suportados os seguintes parâmetros:
  * `%Y` é o ano formatado como `yyyy`
  * `%m` é o mês formatado como `MM`
  * `%d` é o dia formatado como `dd`
  * `%h` é a hora formatada como `HH`
  * `%M` é o minuto formatado como `mm`

Atualmente, o Advisor Métrica suporta o esquema de dados nos ficheiros JSON da seguinte forma. Por exemplo:

``` JSON
[
  {"date": "2018-01-01T00:00:00Z", "market":"en-us", "count":11, "revenue":1.23},
  {"date": "2018-01-01T00:00:00Z", "market":"zh-cn", "count":22, "revenue":4.56}
]
```
<!--
## <span id="eventhubs">Azure Event Hubs</span>

* **Connection String**: This can be found in 'Shared access policies' in your Event Hubs instance. Also for the 'EntityPath', it could be found by clicking into your Event Hubs instance and clicking at 'Event Hubs' in 'Entities' blade. Items that listed can be input as EntityPath. 

* **Consumer Group**: A [consumer group](https://docs.microsoft.com/azure/event-hubs/event-hubs-features#consumer-groups) is a view (state, position, or offset) of an entire event hub.
Event Hubs use the latest offset of a consumer group to consume (subscribe from) the data from data source. Therefore a dedicated consumer group should be created for one data feed in your Metrics Advisor instance.

* **Timestamp**: Metrics Advisor uses the Event Hubs timestamp as the event timestamp if the user data source does not contain a timestamp field.
The timestamp field must match one of these two formats:

    * "YYYY-MM-DDTHH:MM:SSZ" format;

    * Number of seconds or milliseconds from the epoch of 1970-01-01T00:00:00Z.

    No matter which timestamp field it left aligns to granularity.For example, if timestamp is "2019-01-01T00:03:00Z", granularity is 5 minutes, then Metrics Advisor aligns the timestamp to "2019-01-01T00:00:00Z". If the event timestamp is "2019-01-01T00:10:00Z",  Metrics Advisor uses the timestamp directly without any alignment.
-->
## <a name="span-idsqlazure-sql-database--sql-serverspan"></a><span id="sql">Base de Dados Azure SQL / Servidor SQL</span>

* **Cadeia de ligação**: O Metrics Advisor aceita uma [ADO.NET Cadeia de Ligação de Estilo](https://docs.microsoft.com/dotnet/framework/data/adonet/connection-string-syntax) para fonte de dados do servidor sql.

    Cadeia de ligação da amostra:

    ```
    Data Source=db-server.database.windows.net:[port];initial catalog=[database];User ID=[username];Password=[password];Connection Timeout=10ms;
    ```

* **Consulta**: Uma consulta SQL para obter e formular dados em dados multidimensionais da série de tempo. Pode utilizar uma `@StartTime` variável na sua consulta para ajudar a obter o valor esperado das métricas.

  * `@StartTime`: uma data no formato de `yyyy-MM-dd HH:mm:ss`

    Consulta de amostras:
    
    ``` mssql
    select StartDate, JobStatusId, COUNT(*) AS JobNumber from IngestionJobs WHERE and StartDate = @StartTime
    ```
    
    Consulta real executada para a fatia de dados de 2019/12/12:
    
    ``` mssql
    select StartDate, JobStatusId, COUNT(*) AS JobNumber from IngestionJobs WHERE and StartDate = '2019-12-12 00:00:00'
    ```

## <a name="span-idtableazure-table-storagespan"></a><span id="table">Armazenamento de mesa Azure</span>

* **Cadeia de ligação**: Consulte a [Visualização e copie uma cadeia de ligação](https://docs.microsoft.com/azure/storage/common/storage-account-keys-manage?toc=%2Fazure%2Fstorage%2Ftables%2Ftoc.json&tabs=azure-portal#view-account-access-keys) para obter informações sobre como recuperar a cadeia de ligação do Azure Table Storage.

* **Nome da tabela**: Especifique uma tabela para consultar. Isto pode ser encontrado na sua conta de armazenamento Azure. Clique em **Tabelas** na secção **Serviço de Tabelas.**

* **Consulta** Pode usar a `@StartTime` sua consulta. `@StartTime` é substituído por uma cadeia de formato yyy-MM-ddTHH:mm no script.

    ``` mssql
    let StartDateTime = datetime(@StartTime); let EndDateTime = StartDateTime + 1d; 
    SampleTable | where Timestamp >= StartDateTime and Timestamp < EndDateTime | project Timestamp, Market, RPM
    ```

## <a name="span-ideselasticsearchspan"></a><span id="es">Elástico</span>

* **Anfitrião**:Especifique o anfitrião principal do Agrupamento de Elasticsearch.
* **Porta**:Especificar a porta principal do Cluster elasticsearch.
* **Cabeçalho de autorização**:Especifique o valor do cabeçalho de autorização do Cluster elasticsearch.
* **Consulta**:Especificar a consulta para obter dados. O espaço reservado @StartTime é suportado. por exemplo, quando os dados de 2020-06-21T00:00:00Z são ingeridos, @StartTime = 2020-06-21T00:00:00

## <a name="span-idhttphttp-requestspan"></a><span id="http">Pedido HTTP</span>

* **Solicitação URL**: um url HTTP que pode devolver um JSON. Os espaços reservados %Y,%m,%d,%h,%M são suportados: %Y=ano em formato yyyyy, %m=mês em formato MM, %d=dia em formato dd, %h=hora no formato HH, %M=minuto em formato mm. Por exemplo: `http://microsoft.com/ProjectA/%Y/%m/X_%Y-%m-%d-%h-%M`.
* **Pedido método HTTP**: Use GET ou POST.
* **Cabeçalho de pedido**: Pode adicionar autenticação básica. 
* **Solicitação útil**: Apenas a carga útil JSON é suportada. O espaço reservado @StartTime é suportado na carga útil. A resposta deve estar no seguinte formato JSON: [{"timestamp": "2018-01-01T00:00:00Z", "market":"en-us", "count":11, "receita":1.23}, {"timestamp": "2018-01-01T00:00:00Z", "mercado":"zh-cn", "count":22, "receita":4.56}. (por exemplo, quando os dados de 2020-06-21T00:00:00Z são ingeridos, @StartTime = 2020-06-21T00:00:00.0000000+00:00)

## <a name="span-idinfluxdbinfluxdb-influxqlspan"></a><span id="influxdb">InfluxDB (InfluxQL)</span>

* **Cadeia de ligação**: O fio de ligação para aceder ao seu InfluxDB.
* **Base de dados**: A base de dados para consulta.
* **Consulta**: Uma consulta para obter e formular dados em dados multidimensionais de séries de tempo para ingestão.
* **Nome do utilizador**: Isto é opcional para autenticação. 
* **Senha**: Isto é opcional para autenticação. 

## <a name="span-idmongodbmongodbspan"></a><span id="mongodb">MongoDB</span>

* **Cadeia de ligação**: O fio de ligação para aceder ao seu MongoDB.
* **Base de dados**: A base de dados para consulta.
* **Comando**: Um comando para obter e formular dados em dados multidimensionais de séries de tempo para ingestão.

## <a name="span-idmysqlmysqlspan"></a><span id="mysql">MySQL</span>

* **Cadeia de ligação**: O fio de ligação para aceder ao seu MySQL DB.
* **Consulta**: Uma consulta para obter e formular dados em dados multidimensionais de séries de tempo para ingestão.

## <a name="span-idpgsqlpostgresqlspan"></a><span id="pgsql">PostgreSQL</span>

* **Cadeia de ligação**: O fio de ligação para aceder ao seu DB PostgreSQL.
* **Consulta**: Uma consulta para obter e formular dados em dados multidimensionais de séries de tempo para ingestão.

## <a name="next-steps"></a>Passos seguintes

* Enquanto espera que os seus dados métricos sejam ingeridos no sistema, leia sobre [como gerir as configurações de feed de dados](how-tos/manage-data-feeds.md).
* Quando os seus dados métricos são ingeridos, pode [configurar métricas e afinar a configuração de deteção](how-tos/configure-metrics.md).