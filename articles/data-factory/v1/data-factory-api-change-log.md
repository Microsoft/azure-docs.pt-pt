---
title: Data Factory-log de alterações da API .NET
description: Descreve as alterações significativas, adições de recursos, correções de bugs e assim por diante, em uma versão específica da API do .NET para o Azure Data Factory.
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
ms.openlocfilehash: dbbbdebdcf1db7afe485166f5744f2291b757d50
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74979007"
---
# <a name="azure-data-factory---net-api-change-log"></a>Azure Data Factory-log de alterações da API .NET
> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. 

Este artigo fornece informações sobre alterações no Azure Data Factory SDK em uma versão específica. Você pode encontrar o pacote NuGet mais recente para Azure Data Factory [aqui](https://www.nuget.org/packages/Microsoft.Azure.Management.DataFactories)

## <a name="version-4110"></a>4\.11.0 da versão
Adições de recursos:

* Os seguintes tipos de serviço vinculados foram adicionados:
  * [OnPremisesMongoDbLinkedService](https://msdn.microsoft.com/library/mt765129.aspx)
  * [AmazonRedshiftLinkedService](https://msdn.microsoft.com/library/mt765121.aspx)
  * [AwsAccessKeyLinkedService](https://msdn.microsoft.com/library/mt765144.aspx)
* Os seguintes tipos de conjunto de conjuntos foram adicionados:
  * [MongoDbCollectionDataset](https://msdn.microsoft.com/library/mt765145.aspx)
  * [AmazonS3Dataset](https://msdn.microsoft.com/library/mt765112.aspx)
* Os seguintes tipos de origem de cópia foram adicionados:
  * [MongoDbSource](https://msdn.microsoft.com/library/mt765123.aspx)

## <a name="version-4100"></a>4\.10.0 da versão
* As propriedades opcionais a seguir foram adicionadas ao TextFormat:
  * [SkipLineCount](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.textformat.skiplinecount.aspx)
  * [FirstRowAsHeader](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.textformat.firstrowasheader.aspx)
  * [TreatEmptyAsNull](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.textformat.treatemptyasnull.aspx)
* Os seguintes tipos de serviço vinculados foram adicionados:
  * [OnPremisesCassandraLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.onpremisescassandralinkedservice.aspx)
  * [SalesforceLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.salesforcelinkedservice.aspx)
* Os seguintes tipos de conjunto de conjuntos foram adicionados:
  * [OnPremisesCassandraTableDataset](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.onpremisescassandratabledataset.aspx)
* Os seguintes tipos de origem de cópia foram adicionados:
  * [CassandraSource](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.cassandrasource.aspx)
* Adicionar a propriedade [WebServiceInputs](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.azuremlbatchexecutionactivity.webserviceinputs.aspx) a AzureMLBatchExecutionActivity
  * Habilitar a passagem de várias entradas de serviço Web para um experimento Azure Machine Learning

## <a name="version-491"></a>4\.9.1 da versão
### <a name="bug-fix"></a>Correção de bug
* Substitua a autenticação baseada em WebApi para [WebLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.weblinkedservice.authenticationtype.aspx).

## <a name="version-490"></a>4\.9.0 da versão
### <a name="feature-additions"></a>Adições de recursos
* Adicione as propriedades [EnableStaging](https://msdn.microsoft.com/library/mt767916.aspx) e [StagingSettings](https://msdn.microsoft.com/library/mt767918.aspx) ao CopyActivity. Consulte [cópia em etapas](data-factory-copy-activity-performance.md#staged-copy) para obter detalhes sobre o recurso.

### <a name="bug-fix"></a>Correção de bug
* Introduza uma sobrecarga do método [ActivityWindowOperationExtensions. List](https://msdn.microsoft.com/library/mt767915.aspx) , que usa uma instância de [ActivityWindowsByActivityListParameters](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.activitywindowsbyactivitylistparameters.aspx) .
* Marque [WriteBatchSize](https://msdn.microsoft.com/library/dn884293.aspx) e [WriteBatchTimeout](https://msdn.microsoft.com/library/dn884245.aspx) como opcional em CopySink.

## <a name="version-480"></a>4\.8.0 da versão
### <a name="feature-additions"></a>Adições de recursos
* As propriedades opcionais a seguir foram adicionadas ao tipo de atividade de cópia para habilitar o ajuste do desempenho de cópia:
  * [ParallelCopies](https://msdn.microsoft.com/library/mt767910.aspx)
  * [CloudDataMovementUnits](https://msdn.microsoft.com/library/mt767912.aspx)

## <a name="version-470"></a>4\.7.0 da versão
### <a name="feature-additions"></a>Adições de recursos
* Adicionada nova StorageFormat tipo [OrcFormat](https://msdn.microsoft.com/library/mt723391.aspx) tipo para copiar arquivos no formato colunar de linha otimizado (ORC).
* Adicione as propriedades [AllowPolyBase](https://msdn.microsoft.com/library/mt723396.aspx) e PolyBaseSettings ao SqlDWSink.
  * Permite o uso do polybase para copiar dados em SQL Data Warehouse.

## <a name="version-461"></a>4\.6.1 da versão
### <a name="bug-fixes"></a>Correções de Erros
* Corrige a solicitação HTTP para listar as janelas de atividades.
  * Remove o nome do grupo de recursos e o nome do data factory da carga de solicitação.

## <a name="version-460"></a>4\.6.0 da versão
### <a name="feature-additions"></a>Adições de recursos
* As propriedades a seguir foram adicionadas a [pipelineproperties](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.pipelineproperties_properties.aspx):
  * [PipelineMode](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.pipelineproperties.pipelinemode.aspx)
  * [ExpirationTime](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.pipelineproperties.expirationtime.aspx)
  * [Conjuntos de dados](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.pipelineproperties.datasets.aspx)
* As propriedades a seguir foram adicionadas a [PipelineRuntimeInfo](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.common.models.pipelineruntimeinfo.aspx):
  * [PipelineState](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.common.models.pipelineruntimeinfo.pipelinestate.aspx)
* Adicionada nova tipo de [StorageFormat](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.storageformat.aspx) tipo [JsonFormat](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.jsonformat.aspx) para definir conjuntos de dados cujo dado está no formato JSON.

## <a name="version-450"></a>Versão 4.5.0
### <a name="feature-additions"></a>Adições de recursos
* Adição [de operações de lista para a janela de atividade](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.activitywindowoperationsextensions.aspx).
  * Métodos adicionados para recuperar janelas de atividades com filtros baseados nos tipos de entidade (ou seja, data factories, conjuntos de dados, pipelines e atividades).
* Os seguintes tipos de serviço vinculados foram adicionados:
  * [ODataLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.odatalinkedservice.aspx), [WebLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.weblinkedservice.aspx)
* Os seguintes tipos de conjunto de conjuntos foram adicionados:
  * [ODataResourceDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.odataresourcedataset.aspx), [WebTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.webtabledataset.aspx)
* Os seguintes tipos de origem de cópia foram adicionados:     
  * [WebSource](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.websource.aspx)

## <a name="version-440"></a>4\.4.0 da versão
### <a name="feature-additions"></a>Adições de recursos
* O seguinte tipo de serviço vinculado foi adicionado como fontes de dados e coletores para atividades de cópia:
  * [AzureStorageSasLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.azurestoragesaslinkedservice.aspx). Consulte [serviço vinculado de SAS do armazenamento do Azure](data-factory-azure-blob-connector.md#azure-storage-sas-linked-service) para obter informações conceituais e exemplos.

## <a name="version-430"></a>Versão do 4.3.0
### <a name="feature-additions"></a>Adições de recursos
* Os seguintes tipos de serviço vinculados foram foram adicionados como fontes de dados para atividades de cópia:
  * [HdfsLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.hdfslinkedservice.aspx). Consulte [mover dados do HDFS usando data Factory](data-factory-hdfs-connector.md) para obter informações conceituais e exemplos.
  * [OnPremisesOdbcLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.onpremisesodbclinkedservice.aspx). Consulte [mover dados de armazenamentos de dados ODBC usando Azure data Factory](data-factory-odbc-connector.md) para obter informações conceituais e exemplos.

## <a name="version-420"></a>4\.2.0 da versão
### <a name="feature-additions"></a>Adições de recursos
* O seguinte novo tipo de atividade foi adicionado: [AzureMLUpdateResourceActivity](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuremlupdateresourceactivity.aspx). Para obter detalhes sobre a atividade, consulte [atualizando modelos do Azure ml usando a atividade atualizar recurso](data-factory-azure-ml-batch-execution-activity.md).
* Uma nova propriedade opcional [updateResourceEndpoint](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuremllinkedservice.updateresourceendpoint.aspx) foi adicionada à [classe AzureMLLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuremllinkedservice.aspx).
* As propriedades [as](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.datafactorymanagementclient.longrunningoperationinitialtimeout.aspx) e [LongRunningOperationRetryTimeout](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.datafactorymanagementclient.longrunningoperationretrytimeout.aspx) foram adicionadas à classe [datafactorymanagementclient, ao](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.datafactorymanagementclient.aspx) .
* Permitir a configuração de tempos limite para chamadas de cliente para o serviço de Data Factory.

## <a name="version-410"></a>4\.1.0 da versão
### <a name="feature-additions"></a>Adições de recursos
* Os seguintes tipos de serviço vinculados foram adicionados:
  * [AzureDataLakeStoreLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice.aspx)
  * [AzureDataLakeAnalyticsLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice.aspx)
* Os seguintes tipos de atividade foram adicionados:
  * [DataLakeAnalyticsUSQLActivity](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datalakeanalyticsusqlactivity.aspx)
* Os seguintes tipos de conjunto de conjuntos foram adicionados:
  * [AzureDataLakeStoreDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestoredataset.aspx)
* Os seguintes tipos de origem e de coletor para a atividade de cópia foram adicionados:
  * [AzureDataLakeStoreSource](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestoresource.aspx)
  * [AzureDataLakeStoreSink](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestoresink.aspx)

## <a name="version-401"></a>Versão 4.0.1
### <a name="breaking-changes"></a>Alterações interruptivas
As classes a seguir foram renomeadas. Os novos nomes eram os nomes originais das classes antes da versão 4.0.0.

| Nome em 4.0.0 | Nome em 4.0.1 |
|:--- |:--- |
| AzureSqlDataWarehouseDataset |[AzureSqlDataWarehouseTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuresqldatawarehousetabledataset.aspx) |
| AzureSqlDataset |[AzureSqlTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuresqltabledataset.aspx) |
| AzureDataset |[AzureTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuretabledataset.aspx) |
| OracleDataset |[OracleTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.oracletabledataset.aspx) |
| RelationalDataset |[RelationalTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.relationaltabledataset.aspx) |
| SqlServerDataset |[SqlServerTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.sqlservertabledataset.aspx) |

## <a name="version-400"></a>Versão 4.0.0
### <a name="breaking-changes"></a>Alterações interruptivas
* As seguintes classes/interfaces foram renomeadas.

| Nome antigo | Nome novo |
|:--- |:--- |
| ITableOperations |[IDatasetOperations](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.idatasetoperations.aspx) |
| Tabelas |[Dataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.dataset.aspx) |
| Tabelaproperties |[Propriedades](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetproperties.aspx) |
| TableTypeProprerties |[DatasetTypeProperties](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasettypeproperties.aspx) |
| TableCreateOrUpdateParameters |[DatasetCreateOrUpdateParameters](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetcreateorupdateparameters.aspx) |
| TableCreateOrUpdateResponse |[DatasetCreateOrUpdateResponse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetcreateorupdateresponse.aspx) |
| TableGetResponse |[DatasetGetResponse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetgetresponse.aspx) |
| TableListResponse |[DatasetListResponse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetlistresponse.aspx) |
| CreateOrUpdateWithRawJsonContentParameters |[DatasetCreateOrUpdateWithRawJsonContentParameters](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetcreateorupdatewithrawjsoncontentparameters.aspx) |

* Os métodos de **lista** retornam os resultados paginados agora. Se a resposta contiver uma propriedade **Nextlink** não vazia, o aplicativo cliente precisará continuar buscando a próxima página até que todas as páginas sejam retornadas.  Segue-se um exemplo:

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
* A API de pipeline de **lista** retorna apenas o resumo de um pipeline em vez de detalhes completos. Por exemplo, as atividades em um resumo de pipeline contêm apenas o nome e o tipo.

### <a name="feature-additions"></a>Adições de recursos
* A classe [SqlDWSink](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.sqldwsink.aspx) dá suporte a duas novas propriedades, **SliceIdentifierColumnName** e **SqlWriterCleanupScript**, para dar suporte à cópia idempotente para o Azure SQL data warehouse. Consulte o artigo [SQL data warehouse do Azure](data-factory-azure-sql-data-warehouse-connector.md) para obter detalhes sobre essas propriedades.
* Agora damos suporte à execução de procedimento armazenado em relação ao banco de dados SQL do Azure e às fontes de SQL Data Warehouse do Azure como parte da atividade de cópia. As classes [sqlsource](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.sqlsource.aspx) e [SqlDWSource](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.sqldwsource.aspx) têm as seguintes propriedades: **SqlReaderStoredProcedureName** e **storedprocedureparameters**. Consulte os artigos sobre o [banco de dados SQL do Azure](data-factory-azure-sql-connector.md#sqlsource) e [SQL data warehouse do Azure](data-factory-azure-sql-data-warehouse-connector.md#sqldwsource) em Azure.com para obter detalhes sobre essas propriedades.  
