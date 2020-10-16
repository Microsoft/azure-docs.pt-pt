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
ms.openlocfilehash: b7936fcd1e4a629a813c4266920f6c34a15cf9b4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89438947"
---
# <a name="azure-data-factory---net-api-change-log"></a>Azure Data Factory - .NET API change log
> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. 

Este artigo fornece informações sobre alterações à Azure Data Factory SDK numa versão específica. Pode encontrar [aqui](https://www.nuget.org/packages/Microsoft.Azure.Management.DataFactories) o mais recente pacote NuGet para Azure Data Factory

## <a name="version-4110"></a>Versão 4.11.0
Adições de recursos:

* Foram adicionados os seguintes tipos de serviço ligados:
  * [OnPremisesMongoDbLinkedService](https://msdn.microsoft.com/library/mt765129.aspx)
  * [AmazonRedshiftLinkedService](https://msdn.microsoft.com/library/mt765121.aspx)
  * [AwsAccessKeyLinkedService](https://msdn.microsoft.com/library/mt765144.aspx)
* Foram adicionados os seguintes tipos de conjunto de dados:
  * [MongoDbCollectionDataset](https://msdn.microsoft.com/library/mt765145.aspx)
  * [AmazonS3Dataset](https://msdn.microsoft.com/library/mt765112.aspx)
* Foram adicionados os seguintes tipos de origem de cópia:
  * [MongoDbSource](https://msdn.microsoft.com/library/mt765123.aspx)

## <a name="version-4100"></a>Versão 4.10.0
* As seguintes propriedades opcionais foram adicionadas ao TextFormat:
  * [SkipLineCount](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.textformat.skiplinecount.aspx)
  * [FirstRowAsHeader](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.textformat.firstrowasheader.aspx)
  * [TreatEmptyAsNull](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.textformat.treatemptyasnull.aspx)
* Foram adicionados os seguintes tipos de serviço ligados:
  * [OnPremisesCassandraLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.onpremisescassandralinkedservice.aspx)
  * [SalesforceLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.salesforcelinkedservice.aspx)
* Foram adicionados os seguintes tipos de conjunto de dados:
  * [OnPremisesCassandraTableDataset](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.onpremisescassandratabledataset.aspx)
* Foram adicionados os seguintes tipos de origem de cópia:
  * [CassandraSource](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.cassandrasource.aspx)
* Adicionar propriedade [WebServiceInputs](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.azuremlbatchexecutionactivity.webserviceinputs.aspx) a AzureMLBatchExecutionActivity
  * Ativar a passagem de várias entradas de serviço web para uma experiência de Aprendizagem automática Azure

## <a name="version-491"></a>Versão 4.9.1
### <a name="bug-fix"></a>Correção de insetos
* Deprecatar a autenticação baseada no WebApi para [o WebLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.weblinkedservice.authenticationtype.aspx).

## <a name="version-490"></a>Versão 4.9.0
### <a name="feature-additions"></a>Adições de recursos
* Adicionar [Ativar](https://msdn.microsoft.com/library/mt767916.aspx) e [encenarSettings](https://msdn.microsoft.com/library/mt767918.aspx) propriedades para CopyActivity. Consulte [a cópia encenada](data-factory-copy-activity-performance.md#staged-copy) para obter mais detalhes sobre a funcionalidade.

### <a name="bug-fix"></a>Correção de insetos
* Introduza uma sobrecarga de [activityWindowOperationExtensions.List,](https://msdn.microsoft.com/library/mt767915.aspx) que requer uma instância [ActivityWindowsByActivityListParameters.](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.activitywindowsbyactivitylistparameters.aspx)
* Mark [WriteBatchSize](https://msdn.microsoft.com/library/dn884293.aspx) e [WriteBatchTimeout](https://msdn.microsoft.com/library/dn884245.aspx) como opcional em CopySink.

## <a name="version-480"></a>Versão 4.8.0
### <a name="feature-additions"></a>Adições de recursos
* Foram adicionadas as seguintes propriedades opcionais ao tipo de atividade Copy para permitir a afinação do desempenho da cópia:
  * [Paralelas](https://msdn.microsoft.com/library/mt767910.aspx)
  * [CloudDataMovementUnnits](https://msdn.microsoft.com/library/mt767912.aspx)

## <a name="version-470"></a>Versão 4.7.0
### <a name="feature-additions"></a>Adições de recursos
* Adicionou novo tipo de ArmazenamentoFormat [OrcFormat](https://msdn.microsoft.com/library/mt723391.aspx) para copiar ficheiros em formato colunaar de linha otimizado (ORC).
* Adicione propriedades [AllowPolyBase](https://msdn.microsoft.com/library/mt723396.aspx) e PolyBaseSettings ao SqlDWSink.
  * Permite a utilização da PolyBase para copiar dados no Azure Synapse Analytics (anteriormente SQL Data Warehouse).

## <a name="version-461"></a>Versão 4.6.1
### <a name="bug-fixes"></a>Correções de Erros
* Correções HTTP pedido para listagem de janelas de atividade.
  * Remove o nome do grupo de recursos e o nome da fábrica de dados da carga útil do pedido.

## <a name="version-460"></a>Versão 4.6.0
### <a name="feature-additions"></a>Adições de recursos
* Foram adicionadas as seguintes propriedades ao [PipelineProperties:](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.pipelineproperties_properties.aspx)
  * [PipelineMode](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.pipelineproperties.pipelinemode.aspx)
  * [ExpirationTime](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.pipelineproperties.expirationtime.aspx)
  * [Conjuntos de dados](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.pipelineproperties.datasets.aspx)
* As seguintes propriedades foram adicionadas ao [PipelineRuntimeInfo:](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.common.models.pipelineruntimeinfo.aspx)
  * [Estado do Pipeline](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.common.models.pipelineruntimeinfo.pipelinestate.aspx)
* Adicionou novo [tipo JsonFormat](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.storageformat.aspx) do tipo [JsonFormat](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.jsonformat.aspx) para definir conjuntos de dados cujos dados estão no formato JSON.

## <a name="version-450"></a>Versão 4.5.0
### <a name="feature-additions"></a>Adições de recursos
* Operações [de lista adicionadas para janela de atividade.](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.activitywindowoperationsextensions.aspx)
  * Métodos adicionados para recuperar janelas de atividade com filtros baseados nos tipos de entidades (isto é, fábricas de dados, conjuntos de dados, oleodutos e atividades).
* Foram adicionados os seguintes tipos de serviço ligados:
  * [ODataLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.odatalinkedservice.aspx), [WebLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.weblinkedservice.aspx)
* Foram adicionados os seguintes tipos de conjunto de dados:
  * [ODataResourceDataset,](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.odataresourcedataset.aspx) [WebTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.webtabledataset.aspx)
* Foram adicionados os seguintes tipos de origem de cópia:     
  * [WebSource](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.websource.aspx)

## <a name="version-440"></a>Versão 4.4.0
### <a name="feature-additions"></a>Adições de recursos
* O seguinte tipo de serviço ligado foi adicionado como fontes de dados e pias para atividades de cópia:
  * [AzurestoragesasLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.azurestoragesaslinkedservice.aspx). Consulte [o Serviço Ligado SAS de Armazenamento Azure](data-factory-azure-blob-connector.md#azure-storage-sas-linked-service) para obter informações e exemplos conceptuais.

## <a name="version-430"></a>Versão 4.3.0
### <a name="feature-additions"></a>Adições de recursos
* Os seguintes tipos de serviços ligados foram adicionados como fontes de dados para atividades de cópia:
  * [HdfsLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.hdfslinkedservice.aspx). Consulte [os dados do Move do HDFS utilizando a Data Factory](data-factory-hdfs-connector.md) para obter informações e exemplos conceptuais.
  * [OnPremisesOdbcLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.onpremisesodbclinkedservice.aspx). Consulte [os dados do Move Das lojas de dados da ODBC utilizando a Azure Data Factory](data-factory-odbc-connector.md) para obter informações e exemplos conceptuais.

## <a name="version-420"></a>Versão 4.2.0
### <a name="feature-additions"></a>Adições de recursos
* Foi adicionado o seguinte novo tipo de atividade: [AzureMLUpdateResourceActivity](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuremlupdateresourceactivity.aspx). Para obter mais informações sobre a atividade, consulte [os modelos Azure ML de atualização utilizando a Atividade de Recursos de Atualização](data-factory-azure-ml-batch-execution-activity.md).
* Uma nova atualização opcional de [propriedadeResourceEndpoint](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuremllinkedservice.updateresourceendpoint.aspx) foi adicionada à [classe AzureMLLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuremllinkedservice.aspx).
* [LongRunningOperationInitialTimeout](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.datafactorymanagementclient.longrunningoperationinitialtimeout.aspx) e [LongRunningOperationRetryTimeout](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.datafactorymanagementclient.longrunningoperationretrytimeout.aspx) foram adicionados à classe [DataFactoryManagementClient.](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.datafactorymanagementclient.aspx)
* Permitir a configuração dos intervalos para chamadas de clientes para o serviço Data Factory.

## <a name="version-410"></a>Versão 4.1.0
### <a name="feature-additions"></a>Adições de recursos
* Foram adicionados os seguintes tipos de serviço ligados:
  * [AzureDataLakeStoreLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice.aspx)
  * [AzureDataLakeAnalyticsLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice.aspx)
* Foram adicionados os seguintes tipos de atividade:
  * [DataLakeAnalyticsUSQLActivity](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datalakeanalyticsusqlactivity.aspx)
* Foram adicionados os seguintes tipos de conjunto de dados:
  * [AzureDataLakeStoreDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestoredataset.aspx)
* Foram adicionados os seguintes tipos de origem e pia para a atividade de cópia:
  * [AzureDataLakeStoreSource](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestoresource.aspx)
  * [AzureDataLakeStoreSink](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestoresink.aspx)

## <a name="version-401"></a>Versão 4.0.1
### <a name="breaking-changes"></a>Alterações interruptivas
As seguintes aulas foram renomeadas. Os novos nomes eram os nomes originais das aulas antes do lançamento de 4.0.0.

| Nome em 4.0.0 | Nome em 4.0.1 |
|:--- |:--- |
| AzureSqlDataWarehouseDataset |[AzureSqlDataWarehouseTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuresqldatawarehousetabledataset.aspx) |
| AzureSqlDataset |[AzureSqlTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuresqltabledataset.aspx) |
| AzureDataset |[AzureTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuretabledataset.aspx) |
| OracleDataset |[OracleTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.oracletabledataset.aspx) |
| Conjunto de Dados Relacionais |[RelacionalTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.relationaltabledataset.aspx) |
| SqlServerDataset |[SqlServerTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.sqlservertabledataset.aspx) |

## <a name="version-400"></a>Versão 4.0.0
### <a name="breaking-changes"></a>Alterações interruptivas
* As seguintes classes/interfaces foram renomeadas.

| Nome antigo | Nome novo |
|:--- |:--- |
| ITableOperations |[IDatasetOperações](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.idatasetoperations.aspx) |
| Tabela |[Conjunto de dados](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.dataset.aspx) |
| Propostas de Mesa |[DatasetProperties](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetproperties.aspx) |
| TabelaTypeProprerties |[DatasetTypeProperties](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasettypeproperties.aspx) |
| TableCreateOrUpdateParameters |[DatasetCreateOrUpdateParameters](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetcreateorupdateparameters.aspx) |
| TableCreateOrUpdateResponse |[DatasetCreateOrUpdateResponse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetcreateorupdateresponse.aspx) |
| TableGetResponse |[DatasetGetResponse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetgetresponse.aspx) |
| TableListResponse |[DatasetListResponse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetlistresponse.aspx) |
| CreateOrUpdateWithRawJsonContentParameters |[DatasetCreateOrUpdateWithRawJsonContentParameters](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetcreateorupdatewithrawjsoncontentparameters.aspx) |

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
* A classe [SqlDWSink](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.sqldwsink.aspx) suporta duas novas propriedades, **SliceIdentifierColumnName** e **SqlWriterCleanUpScript,** para suportar cópia idempotente para Azure Azure Synapse Analytics. Consulte o artigo [Azure Synapse Analytics](data-factory-azure-sql-data-warehouse-connector.md) para obter detalhes sobre estas propriedades.
* Agora apoiamos o procedimento de execução armazenado contra a Base de Dados Azure SQL e fontes Azure Synapse Analytics como parte da Atividade de Cópia. As classes [SqlSource](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.sqlsource.aspx) e [SqlDWSource](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.sqldwsource.aspx) têm as seguintes propriedades: **SqlReaderStoredProcedureName** e **StoredProcedureParameters**. Consulte a [Base de Dados Azure SQL](data-factory-azure-sql-connector.md#sqlsource) e [artigos Azure Synapse Analytics](data-factory-azure-sql-data-warehouse-connector.md#sqldwsource) sobre Azure.com para obter mais informações sobre estas propriedades.  
