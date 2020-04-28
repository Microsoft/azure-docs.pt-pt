---
title: Fábrica de Dados - .NET API Change Log
description: Descreve alterações de rutura, adições de funcionalidades, correções de bugs, e assim por diante, numa versão específica da .NET API para a Fábrica de Dados Azure.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74979007"
---
# <a name="azure-data-factory---net-api-change-log"></a>Azure Data Factory - .NET API change log
> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. 

Este artigo fornece informações sobre alterações ao Azure Data Factory SDK numa versão específica. Pode encontrar aqui o mais recente pacote NuGet para [a](https://www.nuget.org/packages/Microsoft.Azure.Management.DataFactories) Azure Data Factory

## <a name="version-4110"></a>Versão 4.11.0
Adições de recurso:

* Foram adicionados os seguintes tipos de serviços ligados:
  * [OnPremisesMongoDbLinkedService](https://msdn.microsoft.com/library/mt765129.aspx)
  * [AmazonRedshiftLinkedService](https://msdn.microsoft.com/library/mt765121.aspx)
  * [AwsAccessKeyLinkedService](https://msdn.microsoft.com/library/mt765144.aspx)
* Foram adicionados os seguintes tipos de conjuntos de dados:
  * [MongoDbCollectionDataset](https://msdn.microsoft.com/library/mt765145.aspx)
  * [AmazonS3Dataset](https://msdn.microsoft.com/library/mt765112.aspx)
* Foram adicionados os seguintes tipos de fonte de cópia:
  * [MongoDbSource](https://msdn.microsoft.com/library/mt765123.aspx)

## <a name="version-4100"></a>Versão 4.10.0
* Foram adicionadas as seguintes propriedades opcionais ao TextFormat:
  * [Contagem de linha sintetantes](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.textformat.skiplinecount.aspx)
  * [FirstrowasHeader](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.textformat.firstrowasheader.aspx)
  * [Tratadoemptyasnull](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.textformat.treatemptyasnull.aspx)
* Foram adicionados os seguintes tipos de serviços ligados:
  * [OnPremisesCassandraLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.onpremisescassandralinkedservice.aspx)
  * [SalesforceLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.salesforcelinkedservice.aspx)
* Foram adicionados os seguintes tipos de conjuntos de dados:
  * [OnPremisesCassandraTableDataset](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.onpremisescassandratabledataset.aspx)
* Foram adicionados os seguintes tipos de fonte de cópia:
  * [CassandraSource](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.cassandrasource.aspx)
* Adicione a propriedade [WebServiceInputs](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.azuremlbatchexecutionactivity.webserviceinputs.aspx) à AzureMLBatchExecutionActivity
  * Ativar a passagem de várias inputs de serviço web para uma experiência de Aprendizagem automática Azure

## <a name="version-491"></a>Versão 4.9.1
### <a name="bug-fix"></a>Correção de insetos
* Deprecate a autenticação baseada em WebApi para [WebLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.weblinkedservice.authenticationtype.aspx).

## <a name="version-490"></a>Versão 4.9.0
### <a name="feature-additions"></a>Adições de recurso
* Adicione propriedades [de Habilitação](https://msdn.microsoft.com/library/mt767916.aspx) e [Encenação](https://msdn.microsoft.com/library/mt767918.aspx) à CopyActivity. Consulte [a cópia encenada](data-factory-copy-activity-performance.md#staged-copy) para obter detalhes sobre a funcionalidade.

### <a name="bug-fix"></a>Correção de insetos
* Introduza uma sobrecarga de extensões de [operaçõesOperationExtensions.List,](https://msdn.microsoft.com/library/mt767915.aspx) que tem uma instância [de ActivityWindowsByActivityListParameters.](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.activitywindowsbyactivitylistparameters.aspx)
* Marque o [WriteBatchSize](https://msdn.microsoft.com/library/dn884293.aspx) e o [WriteBatchTimeout](https://msdn.microsoft.com/library/dn884245.aspx) como opcional no CopySink.

## <a name="version-480"></a>Versão 4.8.0
### <a name="feature-additions"></a>Adições de recurso
* Foram adicionadas as seguintes propriedades opcionais ao tipo de atividade copy para permitir a sintonização do desempenho da cópia:
  * [Cópias Paralelas](https://msdn.microsoft.com/library/mt767910.aspx)
  * [Unidades de Movimento de CloudData](https://msdn.microsoft.com/library/mt767912.aspx)

## <a name="version-470"></a>Versão 4.7.0
### <a name="feature-additions"></a>Adições de recurso
* Adicionei novo tipo de Formato de Armazenamento [OrcFormat](https://msdn.microsoft.com/library/mt723391.aspx) para copiar ficheiros em formato colunar de linha otimizado (ORC).
* Adicione as propriedades [allowPolyBase](https://msdn.microsoft.com/library/mt723396.aspx) e PolyBaseSettings ao SqlDWSink.
  * Permite a utilização da PolyBase para copiar dados no Armazém de Dados SQL.

## <a name="version-461"></a>Versão 4.6.1
### <a name="bug-fixes"></a>Correções de Erros
* Corrige o pedido http para listar janelas de atividade.
  * Remove o nome do grupo de recursos e o nome da fábrica de dados da carga útil do pedido.

## <a name="version-460"></a>Versão 4.6.0
### <a name="feature-additions"></a>Adições de recurso
* As seguintes propriedades foram adicionadas à [PipelineProperties:](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.pipelineproperties_properties.aspx)
  * [PipelineMode](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.pipelineproperties.pipelinemode.aspx)
  * [ExpirationTime](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.pipelineproperties.expirationtime.aspx)
  * [Conjuntos de dados](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.pipelineproperties.datasets.aspx)
* As seguintes propriedades foram adicionadas ao [PipelineRuntimeInfo:](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.common.models.pipelineruntimeinfo.aspx)
  * [PipelineState](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.common.models.pipelineruntimeinfo.pipelinestate.aspx)
* Adicionou novo tipo de [Formato de Armazenamento](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.storageformat.aspx) [Tipo JsonFormat](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.jsonformat.aspx) para definir conjuntos de dados cujos dados se encontra em formato JSON.

## <a name="version-450"></a>Versão 4.5.0
### <a name="feature-additions"></a>Adições de recurso
* Operações [de lista adicionadas para janela de atividade](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.activitywindowoperationsextensions.aspx).
  * Métodos adicionados para recuperar janelas de atividade com filtros baseados nos tipos de entidades (isto é, fábricas de dados, conjuntos de dados, oleodutos e atividades).
* Foram adicionados os seguintes tipos de serviços ligados:
  * [ODataLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.odatalinkedservice.aspx), [WebLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.weblinkedservice.aspx)
* Foram adicionados os seguintes tipos de conjuntos de dados:
  * [ODataResourceDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.odataresourcedataset.aspx), [WebTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.webtabledataset.aspx)
* Foram adicionados os seguintes tipos de fonte de cópia:     
  * [WebSource](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.websource.aspx)

## <a name="version-440"></a>Versão 4.4.0
### <a name="feature-additions"></a>Adições de recurso
* O seguinte tipo de serviço ligado foi adicionado como fontes de dados e pias para atividades de cópia:
  * [AzureStorageSasLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.azurestoragesaslinkedservice.aspx). Consulte o Serviço Ligado ao [Armazenamento Azure SAS](data-factory-azure-blob-connector.md#azure-storage-sas-linked-service) para obter informações conceptuais e exemplos.

## <a name="version-430"></a>Versão 4.3.0
### <a name="feature-additions"></a>Adições de recurso
* Os seguintes tipos de serviços ligados foram adicionados como fontes de dados para atividades de cópia:
  * [HdfsLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.hdfslinkedservice.aspx). Consulte [os dados do HDFS utilizando a Data Factory](data-factory-hdfs-connector.md) para informações conceptuais e exemplos.
  * [OnPremisesOdbcLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.onpremisesodbclinkedservice.aspx). Consulte [os dados do Move das lojas de dados ODBC utilizando](data-factory-odbc-connector.md) a Azure Data Factory para informações conceptuais e exemplos.

## <a name="version-420"></a>Versão 4.2.0
### <a name="feature-additions"></a>Adições de recurso
* Foi adicionado o seguinte novo tipo de atividade: [AzureMLUpdateResourceActivity](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuremlupdateresourceactivity.aspx). Para mais detalhes sobre a atividade, consulte [os modelos Update Azure ML utilizando a Atividade de Recursos atualizados](data-factory-azure-ml-batch-execution-activity.md).
* Uma nova atualização opcional da [propriedadeResourceEndpoint](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuremllinkedservice.updateresourceendpoint.aspx) foi adicionada à [classe AzureMLLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuremllinkedservice.aspx).
* As propriedades [LongRunningOperationInitialTimeout](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.datafactorymanagementclient.longrunningoperationinitialtimeout.aspx) e [LongRunningOperationRetryTimeout](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.datafactorymanagementclient.longrunningoperationretrytimeout.aspx) foram adicionadas à classe [DataFactoryManagementClient.](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.datafactorymanagementclient.aspx)
* Permitir a configuração dos prazos para chamadas de clientes para o serviço data Factory.

## <a name="version-410"></a>Versão 4.1.0
### <a name="feature-additions"></a>Adições de recurso
* Foram adicionados os seguintes tipos de serviços ligados:
  * [AzureDataLakeStoreLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice.aspx)
  * [AzureDataLakeAnalyticsLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice.aspx)
* Foram adicionados os seguintes tipos de atividade:
  * [DatalakeAnalyticsUSQLActivity](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datalakeanalyticsusqlactivity.aspx)
* Foram adicionados os seguintes tipos de conjuntos de dados:
  * [Conjunto de DataLakeStoreDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestoredataset.aspx)
* Foram adicionados os seguintes tipos de fonte e pia para a atividade de cópia:
  * [AzureDataLakeStoreSource](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestoresource.aspx)
  * [AzureDataLakeStoreSink](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestoresink.aspx)

## <a name="version-401"></a>Versão 4.0.1
### <a name="breaking-changes"></a>Alterações interruptivas
As seguintes aulas foram renomeadas. Os novos nomes eram os nomes originais das aulas antes do lançamento do 4.0.0.

| Nome em 4.0.0 | Nome em 4.0.1 |
|:--- |:--- |
| AzureSqlDataWarehouseDataset |[AzureSqlDataWarehouseTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuresqldatawarehousetabledataset.aspx) |
| Conjunto de Datação AzureSql |[Conjunto de Datação AzureSqlTable](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuresqltabledataset.aspx) |
| AzureDataset |[Conjunto de Datação AzureTable](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuretabledataset.aspx) |
| OracleDataset |[Conjunto de Datação OracleTable](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.oracletabledataset.aspx) |
| Conjunto de Datação Relacional |[Conjunto de Datação De Tabela Relacional](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.relationaltabledataset.aspx) |
| Conjunto de Datação SqlServer |[Conjunto de Datação SqlServerTable](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.sqlservertabledataset.aspx) |

## <a name="version-400"></a>Versão 4.0.0
### <a name="breaking-changes"></a>Alterações interruptivas
* As seguintes classes/interfaces foram rebatizadas.

| Nome antigo | Nome novo |
|:--- |:--- |
| Operações ITable |[IDatasetOperations](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.idatasetoperations.aspx) |
| Tabela |[Conjunto de dados](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.dataset.aspx) |
| TableProperties |[DatasetProperties](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetproperties.aspx) |
| TableTypeProprerties |[DatasetTypeProperties](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasettypeproperties.aspx) |
| TableCreateOrUpdateParameters |[DatasetCreateOrUpdateParameters](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetcreateorupdateparameters.aspx) |
| Resposta tablecreateorupdate |[DatasetCreateOrUpdateResponse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetcreateorupdateresponse.aspx) |
| Resposta tableget |[DatasetGetResponse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetgetresponse.aspx) |
| Resposta tablelist |[DatasetListResponse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetlistresponse.aspx) |
| CreateOrUpdateWithRawJsonContentParameters |[DatasetCreateOrUpdateWithRawJsonContentParameters](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetcreateorupdatewithrawjsoncontentparameters.aspx) |

* Os métodos **da Lista** devolvem os resultados páginados agora. Se a resposta contiver uma propriedade **NextLink** não vazia, a aplicação do cliente precisa de continuar a buscar a página seguinte até que todas as páginas sejam devolvidas.  Segue-se um exemplo:

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
* **A Lista** de gasodutos API devolve apenas o resumo de um oleoduto em vez de detalhes completos. Por exemplo, as atividades num resumo do gasoduto contêm apenas nome e tipo.

### <a name="feature-additions"></a>Adições de recurso
* A classe [SqlDWSink](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.sqldwsink.aspx) suporta duas novas propriedades, **SliceIdentifierColumnName** e **SqlWriterCleanupScript,** para suportar cópias idempotentes para o Armazém de Dados Azure SQL. Consulte o artigo [do Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md) para obter detalhes sobre estas propriedades.
* Agora apoiamos o procedimento armazenado em execução contra as fontes de Base de Dados Azure SQL e Azure SQL Data Warehouse como parte da Atividade de Cópia. As classes [SqlSource](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.sqlsource.aspx) e [SqlDWSource](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.sqldwsource.aspx) têm as seguintes propriedades: **SqlReaderStoredProcedureName** e **StoredProcedureParameters**. Consulte os artigos [azure SQL](data-factory-azure-sql-connector.md#sqlsource) Database e [Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md#sqldwsource) sobre Azure.com para obter detalhes sobre estas propriedades.  
