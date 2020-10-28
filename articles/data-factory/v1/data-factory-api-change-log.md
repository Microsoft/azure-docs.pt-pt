---
title: Data Factory - .NET API Change Log
description: Descreve alterações de rutura, adições de recursos, correções de bugs, e assim por diante, numa versão específica da API .NET para a Azure Data Factory.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
robots: noindex
ms.date: 01/22/2018
ms.openlocfilehash: 24e468007e0e5ea849ac4d7f945b0aaf6377e580
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92633812"
---
# <a name="azure-data-factory---net-api-change-log"></a>Azure Data Factory - .NET API change log
> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. 

Este artigo fornece informações sobre alterações à Azure Data Factory SDK numa versão específica. Pode encontrar [aqui](https://www.nuget.org/packages/Microsoft.Azure.Management.DataFactories) o mais recente pacote NuGet para Azure Data Factory

## <a name="version-4110"></a>Versão 4.11.0
Adições de recursos:

* Foram adicionados os seguintes tipos de serviço ligados:
  * [OnPremisesMongoDbLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.onpremisesmongodblinkedservice)
  * [AmazonRedshiftLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.amazonredshiftlinkedservice)
  * [AwsAccessKeyLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.awsaccesskeylinkedservice)
* Foram adicionados os seguintes tipos de conjunto de dados:
  * [MongoDbCollectionDataset](/dotnet/api/microsoft.azure.management.datafactories.models.mongodbcollectiondataset)
  * [AmazonS3Dataset](/dotnet/api/microsoft.azure.management.datafactories.models.amazons3dataset)
* Foram adicionados os seguintes tipos de origem de cópia:
  * [MongoDbSource](/dotnet/api/microsoft.azure.management.datafactories.models.mongodbsource)

## <a name="version-4100"></a>Versão 4.10.0
* As seguintes propriedades opcionais foram adicionadas ao TextFormat:
  * [SkipLineCount](/dotnet/api/microsoft.azure.management.datafactories.models.textformat)
  * [FirstRowAsHeader](/dotnet/api/microsoft.azure.management.datafactories.models.textformat)
  * [TreatEmptyAsNull](/dotnet/api/microsoft.azure.management.datafactories.models.textformat)
* Foram adicionados os seguintes tipos de serviço ligados:
  * [OnPremisesCassandraLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.onpremisescassandralinkedservice)
  * [SalesforceLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.salesforcelinkedservice)
* Foram adicionados os seguintes tipos de conjunto de dados:
  * [OnPremisesCassandraTableDataset](/dotnet/api/microsoft.azure.management.datafactories.models.onpremisescassandratabledataset)
* Foram adicionados os seguintes tipos de origem de cópia:
  * [CassandraSource](/dotnet/api/microsoft.azure.management.datafactories.models.cassandrasource)
* Adicionar propriedade [WebServiceInputs](/dotnet/api/microsoft.azure.management.datafactories.models.azuremlbatchexecutionactivity) a AzureMLBatchExecutionActivity
  * Ativar a passagem de várias entradas de serviço web para uma experiência de Aprendizagem automática Azure

## <a name="version-491"></a>Versão 4.9.1
### <a name="bug-fix"></a>Correção de insetos
* Deprecatar a autenticação baseada no WebApi para [o WebLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.weblinkedservice).

## <a name="version-490"></a>Versão 4.9.0
### <a name="feature-additions"></a>Adições de recursos
* Adicionar [Ativar](/dotnet/api/microsoft.azure.management.datafactories.models.copyactivity) e [encenarSettings](/dotnet/api/microsoft.azure.management.datafactories.models.stagingsettings) propriedades para CopyActivity. Consulte [a cópia encenada](data-factory-copy-activity-performance.md#staged-copy) para obter mais detalhes sobre a funcionalidade.

### <a name="bug-fix"></a>Correção de insetos
* Introduza uma sobrecarga de [activityWindowOperationExtensions.List,](/dotnet/api/microsoft.azure.management.datafactories.activitywindowoperationsextensions) que requer uma instância [ActivityWindowsByActivityListParameters.](/dotnet/api/microsoft.azure.management.datafactories.models.activitywindowsbyactivitylistparameters)
* Mark [WriteBatchSize](/dotnet/api/microsoft.azure.management.datafactories.models.copysink) e [WriteBatchTimeout](/dotnet/api/microsoft.azure.management.datafactories.models.copysink) como opcional em CopySink.

## <a name="version-480"></a>Versão 4.8.0
### <a name="feature-additions"></a>Adições de recursos
* Foram adicionadas as seguintes propriedades opcionais ao tipo de atividade Copy para permitir a afinação do desempenho da cópia:
  * [Paralelas](/dotnet/api/microsoft.azure.management.datafactories.models.copyactivity)
  * [CloudDataMovementUnnits](/dotnet/api/microsoft.azure.management.datafactories.models.copyactivity)

## <a name="version-470"></a>Versão 4.7.0
### <a name="feature-additions"></a>Adições de recursos
* Adicionou novo tipo de ArmazenamentoFormat [OrcFormat](/dotnet/api/microsoft.azure.management.datafactories.models.orcformat) para copiar ficheiros em formato colunaar de linha otimizado (ORC).
* Adicione propriedades [AllowPolyBase](/dotnet/api/microsoft.azure.management.datafactories.models.sqldwsink) e PolyBaseSettings ao SqlDWSink.
  * Permite a utilização da PolyBase para copiar dados no Azure Synapse Analytics (anteriormente SQL Data Warehouse).

## <a name="version-461"></a>Versão 4.6.1
### <a name="bug-fixes"></a>Correções de Erros
* Correções HTTP pedido para listagem de janelas de atividade.
  * Remove o nome do grupo de recursos e o nome da fábrica de dados da carga útil do pedido.

## <a name="version-460"></a>Versão 4.6.0
### <a name="feature-additions"></a>Adições de recursos
* Foram adicionadas as seguintes propriedades ao [PipelineProperties:](/dotnet/api/microsoft.azure.management.datafactories.models.pipelineproperties)
  * [PipelineMode](/dotnet/api/microsoft.azure.management.datafactories.models.pipelineproperties)
  * [ExpirationTime](/dotnet/api/microsoft.azure.management.datafactories.models.pipelineproperties)
  * [Conjuntos de dados](/dotnet/api/microsoft.azure.management.datafactories.models.pipelineproperties)
* As seguintes propriedades foram adicionadas ao [PipelineRuntimeInfo:](/dotnet/api/microsoft.azure.management.datafactories.common.models.pipelineruntimeinfo)
  * [Estado do Pipeline](/dotnet/api/microsoft.azure.management.datafactories.common.models.pipelineruntimeinfo)
* Adicionou novo [tipo JsonFormat](/dotnet/api/microsoft.azure.management.datafactories.models.storageformat) do tipo [JsonFormat](/dotnet/api/microsoft.azure.management.datafactories.models.jsonformat) para definir conjuntos de dados cujos dados estão no formato JSON.

## <a name="version-450"></a>Versão 4.5.0
### <a name="feature-additions"></a>Adições de recursos
* Operações [de lista adicionadas para janela de atividade.](/dotnet/api/microsoft.azure.management.datafactories.activitywindowoperationsextensions)
  * Métodos adicionados para recuperar janelas de atividade com filtros baseados nos tipos de entidades (isto é, fábricas de dados, conjuntos de dados, oleodutos e atividades).
* Foram adicionados os seguintes tipos de serviço ligados:
  * [ODataLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.odatalinkedservice), [WebLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.weblinkedservice)
* Foram adicionados os seguintes tipos de conjunto de dados:
  * [ODataResourceDataset,](/dotnet/api/microsoft.azure.management.datafactories.models.odataresourcedataset) [WebTableDataset](/dotnet/api/microsoft.azure.management.datafactories.models.webtabledataset)
* Foram adicionados os seguintes tipos de origem de cópia:     
  * [WebSource](/dotnet/api/microsoft.azure.management.datafactories.models.websource)

## <a name="version-440"></a>Versão 4.4.0
### <a name="feature-additions"></a>Adições de recursos
* O seguinte tipo de serviço ligado foi adicionado como fontes de dados e pias para atividades de cópia:
  * [AzurestoragesasLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.azurestoragesaslinkedservice). Consulte [o Serviço Ligado SAS de Armazenamento Azure](data-factory-azure-blob-connector.md#azure-storage-sas-linked-service) para obter informações e exemplos conceptuais.

## <a name="version-430"></a>Versão 4.3.0
### <a name="feature-additions"></a>Adições de recursos
* Os seguintes tipos de serviços ligados foram adicionados como fontes de dados para atividades de cópia:
  * [HdfsLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.hdfslinkedservice). Consulte [os dados do Move do HDFS utilizando a Data Factory](data-factory-hdfs-connector.md) para obter informações e exemplos conceptuais.
  * [OnPremisesOdbcLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.onpremisesodbclinkedservice). Consulte [os dados do Move Das lojas de dados da ODBC utilizando a Azure Data Factory](data-factory-odbc-connector.md) para obter informações e exemplos conceptuais.

## <a name="version-420"></a>Versão 4.2.0
### <a name="feature-additions"></a>Adições de recursos
* Foi adicionado o seguinte novo tipo de atividade: [AzureMLUpdateResourceActivity](/dotnet/api/microsoft.azure.management.datafactories.models.azuremlupdateresourceactivity). Para obter mais informações sobre a atividade, consulte [os modelos Azure ML de atualização utilizando a Atividade de Recursos de Atualização](data-factory-azure-ml-batch-execution-activity.md).
* Uma nova atualização opcional de [propriedadeResourceEndpoint](/dotnet/api/microsoft.azure.management.datafactories.models.azuremllinkedservice) foi adicionada à [classe AzureMLLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.azuremllinkedservice).
* [LongRunningOperationInitialTimeout](/dotnet/api/microsoft.azure.management.datafactories.datafactorymanagementclient) e [LongRunningOperationRetryTimeout](/dotnet/api/microsoft.azure.management.datafactories.datafactorymanagementclient) foram adicionados à classe [DataFactoryManagementClient.](/dotnet/api/microsoft.azure.management.datafactories.datafactorymanagementclient)
* Permitir a configuração dos intervalos para chamadas de clientes para o serviço Data Factory.

## <a name="version-410"></a>Versão 4.1.0
### <a name="feature-additions"></a>Adições de recursos
* Foram adicionados os seguintes tipos de serviço ligados:
  * [AzureDataLakeStoreLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice)
  * [AzureDataLakeAnalyticsLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice)
* Foram adicionados os seguintes tipos de atividade:
  * [DataLakeAnalyticsUSQLActivity](/dotnet/api/microsoft.azure.management.datafactories.models.datalakeanalyticsusqlactivity)
* Foram adicionados os seguintes tipos de conjunto de dados:
  * [AzureDataLakeStoreDataset](/dotnet/api/microsoft.azure.management.datafactories.models.azuredatalakestoredataset)
* Foram adicionados os seguintes tipos de origem e pia para a atividade de cópia:
  * [AzureDataLakeStoreSource](/dotnet/api/microsoft.azure.management.datafactories.models.azuredatalakestoresource)
  * [AzureDataLakeStoreSink](/dotnet/api/microsoft.azure.management.datafactories.models.azuredatalakestoresink)

## <a name="version-401"></a>Versão 4.0.1
### <a name="breaking-changes"></a>Alterações interruptivas
As seguintes aulas foram renomeadas. Os novos nomes eram os nomes originais das aulas antes do lançamento de 4.0.0.

| Nome em 4.0.0 | Nome em 4.0.1 |
|:--- |:--- |
| AzureSqlDataWarehouseDataset |[AzureSqlDataWarehouseTableDataset](/dotnet/api/microsoft.azure.management.datafactories.models.azuresqldatawarehousetabledataset) |
| AzureSqlDataset |[AzureSqlTableDataset](/dotnet/api/microsoft.azure.management.datafactories.models.azuresqltabledataset) |
| AzureDataset |[AzureTableDataset](/dotnet/api/microsoft.azure.management.datafactories.models.azuretabledataset) |
| OracleDataset |[OracleTableDataset](/dotnet/api/microsoft.azure.management.datafactories.models.oracletabledataset) |
| Conjunto de Dados Relacionais |[RelacionalTableDataset](/dotnet/api/microsoft.azure.management.datafactories.models.relationaltabledataset) |
| SqlServerDataset |[SqlServerTableDataset](/dotnet/api/microsoft.azure.management.datafactories.models.sqlservertabledataset) |

## <a name="version-400"></a>Versão 4.0.0
### <a name="breaking-changes"></a>Alterações interruptivas
* As seguintes classes/interfaces foram renomeadas.

| Nome antigo | Nome novo |
|:--- |:--- |
| ITableOperations |[IDatasetOperações](/dotnet/api/microsoft.azure.management.datafactories.idatasetoperations) |
| Tabela |[Conjunto de dados](/dotnet/api/microsoft.azure.management.datafactories.models.dataset) |
| Propostas de Mesa |[DatasetProperties](/dotnet/api/microsoft.azure.management.datafactories.models.datasetproperties) |
| TabelaTypeProprerties |[DatasetTypeProperties](/dotnet/api/microsoft.azure.management.datafactories.models.datasettypeproperties) |
| TableCreateOrUpdateParameters |[DatasetCreateOrUpdateParameters](/dotnet/api/microsoft.azure.management.datafactories.models.datasetcreateorupdateparameters) |
| TableCreateOrUpdateResponse |[DatasetCreateOrUpdateResponse](/dotnet/api/microsoft.azure.management.datafactories.models.datasetcreateorupdateresponse) |
| TableGetResponse |[DatasetGetResponse](/dotnet/api/microsoft.azure.management.datafactories.models.datasetgetresponse) |
| TableListResponse |[DatasetListResponse](/dotnet/api/microsoft.azure.management.datafactories.models.datasetlistresponse) |
| CreateOrUpdateWithRawJsonContentParameters |[DatasetCreateOrUpdateWithRawJsonContentParameters](/dotnet/api/microsoft.azure.management.datafactories.models.datasetcreateorupdatewithrawjsoncontentparameters) |

* Os métodos **da Lista** devolvem agora os resultados da página. Se a resposta contiver uma propriedade **NextLink** não vazia, a aplicação do cliente tem de continuar a buscar a página seguinte até que todas as páginas sejam devolvidas.  Segue-se um exemplo:

    ```csharp
    PipelineListResponse response = client.Pipelines.List("ResourceGroupName", "DataFactoryName");
    var pipelines = new List<Pipeline>(response.Pipelines);

    string nextLink = response.NextLink;
    while (!string.IsNullOrEmpty(nextLink))
    {
        PipelineListResponse nextResponse = client.Pipelines.ListNext(nextLink);
        pipelines.AddRange(nextResponse.Pipelines);

        nextLink = nextResponse.NextLink;
    }
    ```
* **A API** do gasoduto de lista devolve apenas o resumo de um oleoduto em vez de detalhes completos. Por exemplo, as atividades num resumo do gasoduto contêm apenas o nome e o tipo.

### <a name="feature-additions"></a>Adições de recursos
* A classe [SqlDWSink](/dotnet/api/microsoft.azure.management.datafactories.models.sqldwsink) suporta duas novas propriedades, **SliceIdentifierColumnName** e **SqlWriterCleanUpScript,** para suportar cópia idempotente para Azure Azure Synapse Analytics. Consulte o artigo [Azure Synapse Analytics](data-factory-azure-sql-data-warehouse-connector.md) para obter detalhes sobre estas propriedades.
* Agora apoiamos o procedimento de execução armazenado contra a Base de Dados Azure SQL e fontes Azure Synapse Analytics como parte da Atividade de Cópia. As classes [SqlSource](/dotnet/api/microsoft.azure.management.datafactories.models.sqlsource) e [SqlDWSource](/dotnet/api/microsoft.azure.management.datafactories.models.sqldwsource) têm as seguintes propriedades: **SqlReaderStoredProcedureName** e **StoredProcedureParameters** . Consulte a [Base de Dados Azure SQL](data-factory-azure-sql-connector.md#sqlsource) e [artigos Azure Synapse Analytics](data-factory-azure-sql-data-warehouse-connector.md#sqldwsource) sobre Azure.com para obter mais informações sobre estas propriedades.