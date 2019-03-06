---
title: Copiar dados em massa com o Azure Data Factory | Microsoft Docs
description: Saiba como utilizar o Azure Data Factory e a Atividade de Cópia para copiar dados de um arquivo de dados de origem para um arquivo de dados de destino em massa.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/22/2018
ms.author: jingwang
ms.openlocfilehash: 718e34cdba31b3b747ebb5c10f5c5708c0572448
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57436600"
---
# <a name="copy-multiple-tables-in-bulk-by-using-azure-data-factory"></a>Copiar várias tabelas em massa através do Azure Data Factory
Este tutorial demonstra como **copiar várias tabelas da Base de Dados SQL do Azure para o Azure SQL Data Warehouse**. Também pode aplicar o mesmo padrão noutros cenários de cópia. Por exemplo, copiar tabelas do SQL Server/Oracle para a Base de Dados SQL do Azure/Data Warehouse/Blob do Azure, copiar caminhos diferentes do Blob para tabelas de Base de Dados SQL do Azure.

A um nível elevado, este tutorial envolve os seguintes passos:

> [!div class="checklist"]
> * Criar uma fábrica de dados.
> * Criar serviços ligados da Base de Dados SQL do Azure, do Azure SQL Data Warehouse e do Armazenamento do Azure.
> * Criar conjuntos de dados da Base de Dados SQL do Azure e do Azure SQL Data Warehouse.
> * Criar um pipeline para procurar as tabelas a copiar e outro pipeline para executar a operação de cópia real. 
> * Iniciar uma execução de pipeline.
> * Monitorizar o pipeline e execuções de atividades.

Este tutorial utiliza o Azure PowerShell. Para saber mais sobre como utilizar outras ferramentas/SDKs para criar uma fábrica de dados, veja [Inícios rápidos](quickstart-create-data-factory-dot-net.md). 

## <a name="end-to-end-workflow"></a>Fluxo de trabalho ponto a ponto
Neste cenário, temos várias tabelas na Base de Dados SQL do Azure que queremos copiar para o SQL Data Warehouse. Segue-se a sequência lógica de passos no fluxo de trabalho que ocorre nos pipelines:

![Fluxo de trabalho](media/tutorial-bulk-copy/tutorial-copy-multiple-tables.png)

* O primeiro pipeline procura a lista de tabelas que têm de ser copiadas para os arquivos de dados de sink.  Em alternativa, pode manter uma tabela de metadados que apresenta uma lista de todas as tabelas a copiar para o arquivo de dados de sink. Em seguida, o pipeline aciona outro pipeline, que itera cada tabela na base de dados e executa a operação de cópia de dados.
* O segundo pipeline executa a cópia real. Aceita a lista de tabelas como um parâmetro. Para cada tabela na lista, copie a tabela específica na Base de Dados SQL do Azure para a tabela correspondente no SQL Data Warehouse, utilizando [cópia faseada através do Armazenamento de blobs e do PolyBase](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-sql-data-warehouse) para um melhor desempenho. Neste exemplo, o primeiro pipeline passa a lista de tabelas como um valor para o parâmetro. 

Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* **Azure PowerShell**. Siga as instruções em [How to install and configure Azure PowerShell (Como instalar e configurar o Azure PowerShell)](/powershell/azure/install-Az-ps).
* **Conta de Armazenamento do Azure**. A conta de Armazenamento do Azure é utilizada como armazenamento de blobs de teste na operação de cópia em massa. 
* **Base de Dados SQL do Azure**. Esta base de dados contém os dados de origem. 
* **Azure SQL Data Warehouse**. Este armazém de dados contém os dados copiados da Base de Dados SQL. 

### <a name="prepare-sql-database-and-sql-data-warehouse"></a>Preparar a Base de Dados SQL e o SQL Data Warehouse

**Preparar a Base de Dados SQL do Azure de origem**:

Crie uma Base de Dados SQL do Azure SQL com dados de exemplo do Adventure Works LT, seguindo o artigo [Criar uma base de dados SQL do Azure](../sql-database/sql-database-get-started-portal.md). Este tutorial copia todas as tabelas desta base de dados de exemplo para um SQL Data Warehouse.

**Preparar o Azure SQL Data Warehouse de sink**:

1. Se não tiver um Azure SQL Data Warehouse, veja o artigo [Criar um SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-get-started-tutorial.md) para obter os passos para criar um.

2. Crie esquemas de tabela correspondentes no SQL Data Warehouse. Pode utilizar o [Utilitário de Migração](https://www.microsoft.com/download/details.aspx?id=49100) para **migrar o esquema** da Base de Dados SQL do Azure para o Azure SQL Data Warehouse. Irá utilizar o Azure Data Factory para migrar/copiar dados num passo mais à frente.

## <a name="azure-services-to-access-sql-server"></a>Serviços do Azure para aceder ao SQL Server

Tanto para a Base de dados SQL como para o SQL Data Warehouse, permita que os serviços do Azure acedam ao SQL Server. Certifique-se de que a definição **Permitir acesso aos serviços do Azure** está **ativada** para o seu SQL Server do Azure. Esta definição permite ao serviço Data Factory ler dados da Base de Dados SQL do Azure e escrever dados no Azure SQL Data Warehouse. Para verificar e ativar desta definição, execute os passos seguintes:

1. Clique em **Todos os serviços** à esquerda e clique em **Servidores SQL**.
2. Selecione o seu servidor e clique em **Firewall** em **DEFINIÇÕES**.
3. Na página **Definições de firewall**, clique em **ATIVAR** para **Permitir acesso aos serviços do Azure**.

## <a name="create-a-data-factory"></a>Criar uma fábrica de dados

1. Inicie o **Azure PowerShell**. Mantenha o Azure PowerShell aberto até ao fim deste tutorial. Se o fechar e reabrir, terá de executar os comandos novamente.

    Execute o comando seguinte e introduza o nome de utilizador e a palavra-passe que utiliza para iniciar sessão no Portal do Azure:
        
    ```powershell
    Connect-AzAccount
    ```
    Execute o comando seguinte para ver todas as subscrições desta conta:

    ```powershell
    Get-AzSubscription
    ```
    Execute o comando seguinte para selecionar a subscrição com a qual pretende trabalhar. Substitua **SubscriptionId** pelo ID da sua subscrição do Azure:

    ```powershell
    Select-AzSubscription -SubscriptionId "<SubscriptionId>"
    ```
2. Executar o **Set-AzDataFactoryV2** cmdlet para criar uma fábrica de dados. Substitua os marcadores de posição pelos seus próprios valores antes de executar o comando. 

    ```powershell
    $resourceGroupName = "<your resource group to create the factory>"
    $dataFactoryName = "<specify the name of data factory to create. It must be globally unique.>"
    Set-AzDataFactoryV2 -ResourceGroupName $resourceGroupName -Location "East US" -Name $dataFactoryName
    ```

    Tenha em atenção os seguintes pontos:

    * O nome do Azure Data Factory deve ser globalmente exclusivo. Se receber o erro seguinte, altere o nome e tente novamente.

        ```
        The specified Data Factory name 'ADFv2QuickStartDataFactory' is already in use. Data Factory names must be globally unique.
        ```

    * Para criar instâncias do Data Factory, tem de ser Contribuidor ou Administrador da subscrição do Azure.
    * Para obter uma lista de regiões do Azure em que a fábrica de dados está atualmente disponível, selecione as regiões que lhe interessam, na página seguinte e, em seguida, expanda **Analytics** para localizar **Data Factory**: [Produtos disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/). Os arquivos de dados (Armazenamento do Azure, Base de Dados SQL do Azure, etc.) e as computações (HDInsight, etc.) utilizados pela fábrica de dados podem estar noutras regiões.

## <a name="create-linked-services"></a>Criar serviços ligados

Neste tutorial, vai criar três serviços ligados para blob de origem, sink e teste, respetivamente, que incluem ligações para os arquivos de dados:

### <a name="create-the-source-azure-sql-database-linked-service"></a>Criar o serviço ligado da Base de Dados do Azure de origem

1. Crie um ficheiro JSON com o nome **Azuresqldatabaselinkedservice** na **C:\ADFv2TutorialBulkCopy** pasta com o seguinte conteúdo: (Crie a pasta ADFv2TutorialBulkCopy caso ainda não exista.)

    > [!IMPORTANT]
    > Substitua &lt;servername&gt;, &lt;databasename&gt;, &lt;username&gt;@&lt;servername&gt; e &lt;password&gt; pelos valores da sua Base de Dados SQL do Azure antes de guardar o ficheiro.

    ```json
    {
        "name": "AzureSqlDatabaseLinkedService",
        "properties": {
            "type": "AzureSqlDatabase",
            "typeProperties": {
                "connectionString": {
                    "type": "SecureString",
                    "value": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
                }
            }
        }
    }
    ```

2. No **Azure PowerShell**, mude para a pasta **ADFv2TutorialBulkCopy**.

3. Executar o **Set-AzDataFactoryV2LinkedService** cmdlet para criar o serviço ligado: **AzureSqlDatabaseLinkedService**. 

    ```powershell
    Set-AzDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureSqlDatabaseLinkedService" -File ".\AzureSqlDatabaseLinkedService.json"
    ```

    Segue-se o resultado do exemplo:

    ```json
    LinkedServiceName : AzureSqlDatabaseLinkedService
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlDatabaseLinkedService
    ```

### <a name="create-the-sink-azure-sql-data-warehouse-linked-service"></a>Criar o serviço ligado do Azure SQL Data Warehouse de sink

1. Crie um ficheiro JSON com o nome **AzureSqlDWLinkedService.json** na pasta **C:\ADFv2TutorialBulkCopy**, com o seguinte conteúdo:

    > [!IMPORTANT]
    > Substitua &lt;servername&gt;, &lt;databasename&gt;, &lt;username&gt;@&lt;servername&gt; e &lt;password&gt; pelos valores da sua Base de Dados SQL do Azure antes de guardar o ficheiro.

    ```json
    {
        "name": "AzureSqlDWLinkedService",
        "properties": {
            "type": "AzureSqlDW",
            "typeProperties": {
                "connectionString": {
                    "type": "SecureString",
                    "value": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
            }
            }
        }
    }
    ```

2. Para criar o serviço ligado: **AzureSqlDWLinkedService**, execute o **conjunto AzDataFactoryV2LinkedService** cmdlet.

    ```powershell
    Set-AzDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureSqlDWLinkedService" -File ".\AzureSqlDWLinkedService.json"
    ```

    Segue-se o resultado do exemplo:

    ```json
    LinkedServiceName : AzureSqlDWLinkedService
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlDWLinkedService
    ```

### <a name="create-the-staging-azure-storage-linked-service"></a>Criar o serviço ligado de Armazenamento do Azure de teste

Neste tutorial, vai utilizar o armazenamento de Blobs do Azure como área de teste provisória para ativar o PolyBase para um melhor desempenho de cópia.

1. Crie um ficheiro JSON com o nome **AzureStorageLinkedService.json** na pasta **C:\ADFv2TutorialBulkCopy**, com o seguinte conteúdo:

    > [!IMPORTANT]
    > Substitua &lt;accountName&gt; e &lt;accountKey&gt; pelo nome e chave da sua conta de armazenamento do Azure antes de guardar o ficheiro.

    ```json
    {
        "name": "AzureStorageLinkedService",
        "properties": {
            "type": "AzureStorage",
            "typeProperties": {
                "connectionString": {
                    "type": "SecureString",
                    "value": "DefaultEndpointsProtocol=https;AccountName=<accountName>;AccountKey=<accountKey>"
                }
            }
        }
    }
    ```

2. Para criar o serviço ligado: **AzureStorageLinkedService**, execute o **conjunto AzDataFactoryV2LinkedService** cmdlet.

    ```powershell
    Set-AzDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureStorageLinkedService" -File ".\AzureStorageLinkedService.json"
    ```

    Segue-se o resultado do exemplo:

    ```json
    LinkedServiceName : AzureStorageLinkedService
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureStorageLinkedService
    ```

## <a name="create-datasets"></a>Criar conjuntos de dados

Neste tutorial, vai criar conjuntos de dados de origem e sink, que especificam a localização onde os dados são armazenados:

### <a name="create-a-dataset-for-source-sql-database"></a>Criar um conjunto de dados para a Base de Dados SQL de origem

1. Crie um ficheiro JSON com o nome **AzureSqlDatabaseDataset.json** na pasta **C:\ADFv2TutorialBulkCopy**, com o seguinte conteúdo. "tableName" é um nome fictício, uma vez que mais à frente vai utilizar a consulta SQL na atividade de cópia para obter dados.

    ```json
    {
        "name": "AzureSqlDatabaseDataset",
        "properties": {
            "type": "AzureSqlTable",
            "linkedServiceName": {
                "referenceName": "AzureSqlDatabaseLinkedService",
                "type": "LinkedServiceReference"
            },
            "typeProperties": {
                "tableName": "dummy"
            }
        }
    }
    ```

2. Para criar o conjunto de dados: **AzureSqlDatabaseDataset**, execute o **conjunto AzDataFactoryV2Dataset** cmdlet.

    ```powershell
    Set-AzDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureSqlDatabaseDataset" -File ".\AzureSqlDatabaseDataset.json"
    ```

    Segue-se o resultado do exemplo:

    ```json
    DatasetName       : AzureSqlDatabaseDataset
    ResourceGroupName : <resourceGroupname>
    DataFactoryName   : <dataFactoryName>
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlTableDataset
    ```

### <a name="create-a-dataset-for-sink-sql-data-warehouse"></a>Criar um conjunto de dados para o SQL Data Warehouse de sink

1. Crie um ficheiro JSON com o nome **Azuresqldwdataset** no **C:\ADFv2TutorialBulkCopy** pasta, com o seguinte conteúdo: "tableName" está definido como um parâmetro, mais tarde a atividade de cópia que faz referência a este conjunto de dados transmite o valor real para o conjunto de dados.

    ```json
    {
        "name": "AzureSqlDWDataset",
        "properties": {
            "type": "AzureSqlDWTable",
            "linkedServiceName": {
                "referenceName": "AzureSqlDWLinkedService",
                "type": "LinkedServiceReference"
            },
            "typeProperties": {
                "tableName": {
                    "value": "@{dataset().DWTableName}",
                    "type": "Expression"
                }
            },
            "parameters":{
                "DWTableName":{
                    "type":"String"
                }
            }
        }
    }
    ```

2. Para criar o conjunto de dados: **AzureSqlDWDataset**, execute o **conjunto AzDataFactoryV2Dataset** cmdlet.

    ```powershell
    Set-AzDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureSqlDWDataset" -File ".\AzureSqlDWDataset.json"
    ```

    Segue-se o resultado do exemplo:

    ```json
    DatasetName       : AzureSqlDWDataset
    ResourceGroupName : <resourceGroupname>
    DataFactoryName   : <dataFactoryName>
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlDwTableDataset
    ```

## <a name="create-pipelines"></a>Criar pipelines

Neste tutorial, vai criar dois pipelines:

### <a name="create-the-pipeline-iterateandcopysqltables"></a>Criar o pipeline "IterateAndCopySQLTables"

Este pipeline aceita uma lista de tabelas como parâmetro. Para cada tabela na lista, copia dados da tabela na Base de Dados SQL do Azure para o Azure SQL Data Warehouse, utilizando a cópia faseada e o PolyBase.

1. Crie um ficheiro JSON com o nome **IterateAndCopySQLTables.json** na pasta **C:\ADFv2TutorialBulkCopy**, com o seguinte conteúdo:

    ```json
    {
        "name": "IterateAndCopySQLTables",
        "properties": {
            "activities": [
                {
                    "name": "IterateSQLTables",
                    "type": "ForEach",
                    "typeProperties": {
                        "isSequential": "false",
                        "items": {
                            "value": "@pipeline().parameters.tableList",
                            "type": "Expression"
                        },
                        "activities": [
                            {
                                "name": "CopyData",
                                "description": "Copy data from SQL database to SQL DW",
                                "type": "Copy",
                                "inputs": [
                                    {
                                        "referenceName": "AzureSqlDatabaseDataset",
                                        "type": "DatasetReference"
                                    }
                                ],
                                "outputs": [
                                    {
                                        "referenceName": "AzureSqlDWDataset",
                                        "type": "DatasetReference",
                                        "parameters": {
                                            "DWTableName": "[@{item().TABLE_SCHEMA}].[@{item().TABLE_NAME}]"
                                        }
                                    }
                                ],
                                "typeProperties": {
                                    "source": {
                                        "type": "SqlSource",
                                        "sqlReaderQuery": "SELECT * FROM [@{item().TABLE_SCHEMA}].[@{item().TABLE_NAME}]"
                                    },
                                    "sink": {
                                        "type": "SqlDWSink",
                                        "preCopyScript": "TRUNCATE TABLE [@{item().TABLE_SCHEMA}].[@{item().TABLE_NAME}]",
                                        "allowPolyBase": true
                                    },
                                    "enableStaging": true,
                                    "stagingSettings": {
                                        "linkedServiceName": {
                                            "referenceName": "AzureStorageLinkedService",
                                            "type": "LinkedServiceReference"
                                        }
                                    }
                                }
                            }
                        ]
                    }
                }
            ],
            "parameters": {
                "tableList": {
                    "type": "Object"
                }
            }
        }
    }
    ```

2. Para criar o pipeline: **IterateAndCopySQLTables**, execute o **conjunto AzDataFactoryV2Pipeline** cmdlet.

    ```powershell
    Set-AzDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "IterateAndCopySQLTables" -File ".\IterateAndCopySQLTables.json"
    ```

    Segue-se o resultado do exemplo:

    ```json
    PipelineName      : IterateAndCopySQLTables
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Activities        : {IterateSQLTables}
    Parameters        : {[tableList, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification]}
    ```

### <a name="create-the-pipeline-gettablelistandtriggercopydata"></a>Criar o pipeline "GetTableListAndTriggerCopyData"

Este pipeline executa dois passos:

* Procura a tabela do sistema da Base de Dados SQL do Azure para obter a lista de tabelas a copiar.
* Aciona o pipeline "IterateAndCopySQLTables" para executar a cópia de dados real.

1. Crie um ficheiro JSON com o nome **GetTableListAndTriggerCopyData.json** na pasta **C:\ADFv2TutorialBulkCopy**, com o seguinte conteúdo:

    ```json
    {
        "name":"GetTableListAndTriggerCopyData",
        "properties":{
            "activities":[
                { 
                    "name": "LookupTableList",
                    "description": "Retrieve the table list from Azure SQL dataabse",
                    "type": "Lookup",
                    "typeProperties": {
                        "source": {
                            "type": "SqlSource",
                            "sqlReaderQuery": "SELECT TABLE_SCHEMA, TABLE_NAME FROM information_schema.TABLES WHERE TABLE_TYPE = 'BASE TABLE' and TABLE_SCHEMA = 'SalesLT' and TABLE_NAME <> 'ProductModel'"
                        },
                        "dataset": {
                            "referenceName": "AzureSqlDatabaseDataset",
                            "type": "DatasetReference"
                        },
                        "firstRowOnly": false
                    }
                },
                {
                    "name": "TriggerCopy",
                    "type": "ExecutePipeline",
                    "typeProperties": {
                        "parameters": {
                            "tableList": {
                                "value": "@activity('LookupTableList').output.value",
                                "type": "Expression"
                            }
                        },
                        "pipeline": {
                            "referenceName": "IterateAndCopySQLTables",
                            "type": "PipelineReference"
                        },
                        "waitOnCompletion": true
                    },
                    "dependsOn": [
                        {
                            "activity": "LookupTableList",
                            "dependencyConditions": [
                                "Succeeded"
                            ]
                        }
                    ]
                }
            ]
        }
    }
    ```

2. Para criar o pipeline: **GetTableListAndTriggerCopyData**, execute o **conjunto AzDataFactoryV2Pipeline** cmdlet.

    ```powershell
    Set-AzDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "GetTableListAndTriggerCopyData" -File ".\GetTableListAndTriggerCopyData.json"
    ```

    Segue-se o resultado do exemplo:

    ```json
    PipelineName      : GetTableListAndTriggerCopyData
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Activities        : {LookupTableList, TriggerCopy}
    Parameters        :
    ```

## <a name="start-and-monitor-pipeline-run"></a>Iniciar e monitorizar uma execução de pipeline

1. Inicie uma execução de pipeline para o pipeline "GetTableListAndTriggerCopyData" principal e capture o ID de execução de pipeline para futura monitorização. Por baixo, aciona a execução do pipeline "IterateAndCopySQLTables", conforme especificado na atividade ExecutePipeline.

    ```powershell
    $runId = Invoke-AzDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineName 'GetTableListAndTriggerCopyData'
    ```

2.  Execute o script seguinte para verificar continuamente o estado de execução do pipeline **GetTableListAndTriggerCopyData** e imprima a execução de pipeline final e o resultado da execução de atividade.

    ```powershell
    while ($True) {
        $run = Get-AzDataFactoryV2PipelineRun -ResourceGroupName $resourceGroupName -DataFactoryName $DataFactoryName -PipelineRunId $runId

        if ($run) {
            if ($run.Status -ne 'InProgress') {
                Write-Host "Pipeline run finished. The status is: " $run.Status -foregroundcolor "Yellow"
                Write-Host "Pipeline run details:" -foregroundcolor "Yellow"
                $run
                break
            }
            Write-Host  "Pipeline is running...status: InProgress" -foregroundcolor "Yellow"
        }

        Start-Sleep -Seconds 15
    }

    $result = Get-AzDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId $runId -RunStartedAfter (Get-Date).AddMinutes(-30) -RunStartedBefore (Get-Date).AddMinutes(30)
    Write-Host "Activity run details:" -foregroundcolor "Yellow"
    $result
    ```

    Eis o resultado da execução de exemplo:

    ```json
    Pipeline run details:
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    RunId             : 0000000000-00000-0000-0000-000000000000
    PipelineName      : GetTableListAndTriggerCopyData
    LastUpdated       : 9/18/2017 4:08:15 PM
    Parameters        : {}
    RunStart          : 9/18/2017 4:06:44 PM
    RunEnd            : 9/18/2017 4:08:15 PM
    DurationInMs      : 90637
    Status            : Succeeded
    Message           : 

    Activity run details:
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    ActivityName      : LookupTableList
    PipelineRunId     : 0000000000-00000-0000-0000-000000000000
    PipelineName      : GetTableListAndTriggerCopyData
    Input             : {source, dataset, firstRowOnly}
    Output            : {count, value, effectiveIntegrationRuntime}
    LinkedServiceName : 
    ActivityRunStart  : 9/18/2017 4:06:46 PM
    ActivityRunEnd    : 9/18/2017 4:07:09 PM
    DurationInMs      : 22995
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}

    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    ActivityName      : TriggerCopy
    PipelineRunId     : 0000000000-00000-0000-0000-000000000000
    PipelineName      : GetTableListAndTriggerCopyData
    Input             : {pipeline, parameters, waitOnCompletion}
    Output            : {pipelineRunId}
    LinkedServiceName : 
    ActivityRunStart  : 9/18/2017 4:07:11 PM
    ActivityRunEnd    : 9/18/2017 4:08:14 PM
    DurationInMs      : 62581
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}
    ```

3. Pode obter o ID de execução do pipeline "**IterateAndCopySQLTables**" e verificar o resultado da execução de atividade detalhada da seguinte forma.

    ```powershell
    Write-Host "Pipeline 'IterateAndCopySQLTables' run result:" -foregroundcolor "Yellow"
    ($result | Where-Object {$_.ActivityName -eq "TriggerCopy"}).Output.ToString()
    ```

    Eis o resultado da execução de exemplo:

    ```json
    {
        "pipelineRunId": "7514d165-14bf-41fb-b5fb-789bea6c9e58"
    }
    ```

    ```powershell
    $result2 = Get-AzDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId <copy above run ID> -RunStartedAfter (Get-Date).AddMinutes(-30) -RunStartedBefore (Get-Date).AddMinutes(30)
    $result2
    ```

3. Ligue ao Azure SQL Data Warehouse de sink e confirme se os dados foram copiados corretamente da Base de Dados SQL do Azure.

## <a name="next-steps"></a>Passos Seguintes
Neste tutorial, executou os passos seguintes: 

> [!div class="checklist"]
> * Criar uma fábrica de dados.
> * Criar serviços ligados da Base de Dados SQL do Azure, do Azure SQL Data Warehouse e do Armazenamento do Azure.
> * Criar conjuntos de dados da Base de Dados SQL do Azure e do Azure SQL Data Warehouse.
> * Criar um pipeline para procurar as tabelas a copiar e outro pipeline para executar a operação de cópia real. 
> * Iniciar uma execução de pipeline.
> * Monitorizar o pipeline e execuções de atividades.

Avance para o tutorial seguinte para saber como copiar dados de forma incremental de uma origem para um destino:
> [!div class="nextstepaction"]
>[Copiar dados de forma incremental](tutorial-incremental-copy-powershell.md)
