---
title: Copiar uma tabela de forma incremental com o Azure Data Factory | Microsoft Docs
description: Neste tutorial, vai criar um pipeline da fábrica de dados do Azure, que copia dados de forma incremental de uma base de dados SQL do Azure para o armazenamento de Blobs do Azure.
services: data-factory
documentationcenter: ''
author: dearandyxu
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/22/2018
ms.author: yexu
ms.openlocfilehash: 568b00007f2c95a5a63c236863f0c599c6b6f86f
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57992298"
---
# <a name="incrementally-load-data-from-an-azure-sql-database-to-azure-blob-storage"></a>Carregar dados de forma incremental de uma base de dados SQL do Azure para o armazenamento de Blobs do Azure
Neste tutorial, vai criar uma fábrica de dados do Azure com um pipeline que carrega dados delta de uma tabela numa base de dados SQL do Azure para o armazenamento de Blobs do Azure. 

Vai executar os seguintes passos neste tutorial:

> [!div class="checklist"]
> * Preparar o arquivo de dados para armazenar o valor de limite de tamanho.
> * Criar uma fábrica de dados.
> * Criar serviços ligados. 
> * Crie conjuntos de dados de origem, de sink e de limite de tamanho.
> * Criar um pipeline.
> * Executar o pipeline.
> * Monitorizar a execução do pipeline. 

## <a name="overview"></a>Descrição geral
Eis o diagrama da solução de alto nível: 

![Carregar dados de forma incremental](media/tutorial-Incrementally-copy-powershell/incrementally-load.png)

Eis os passos importantes para criar esta solução: 

1. **Selecionar a coluna de limite de tamanho**.
    Selecione uma coluna no arquivo de dados de origem, que pode ser utilizada para dividir os registos novos ou atualizados para cada execução. Normalmente, os dados nesta coluna selecionada (por exemplo, last_modify_time ou ID) continuam a aumentar quando as linhas são criadas ou atualizadas. O valor máximo nesta coluna é utilizado como limite de tamanho.

2. **Preparar um arquivo de dados para armazenar o valor de limite de tamanho**.   
    Neste tutorial, vai armazenar o valor de limite superior numa base de dados SQL.
    
3. **Crie um pipeline com o seguinte fluxo de trabalho**: 
    
    O pipeline nesta solução tem as seguintes atividades:
  
    * Criar duas atividades Lookup. Utilize a primeira atividade Lookup para obter o último valor de limite de tamanho. Utilize a segunda para obter o valor de limite de tamanho novo. Estes valores de limite de tamanho são transmitidos para a atividade Copy. 
    * Criar uma atividade Copy que copia linhas do arquivo de dados de origem com o valor da coluna de limite de tamanho superior ao valor de limite de tamanho antigo e inferior ao valor novo. Em seguida, copia os dados delta do arquivo de dados de origem para um armazenamento de Blobs como um ficheiro novo. 
    * Criar uma atividade StoredProcedure, que atualiza o valor de limite de tamanho do pipeline que vai ser executado da próxima vez. 


Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* **Base de Dados SQL do Azure**. Vai utilizar a base de dados como o arquivo de dados de origem. Se não tiver uma base de dados SQL, veja [Criar uma base de dados SQL do Azure](../sql-database/sql-database-get-started-portal.md) para seguir os passos para criar uma.
* **Armazenamento do Azure**. Vai utilizar o armazenamento de blobs como arquivo de dados de sink. Se não tiver uma conta de armazenamento, veja [Criar uma conta de armazenamento](../storage/common/storage-quickstart-create-account.md) para seguir os passos para criar uma. Crie um contentor com o nome adftutorial. 
* **Azure PowerShell**. Siga as instruções em [Instalar e configurar o Azure PowerShell](/powershell/azure/install-Az-ps).

### <a name="create-a-data-source-table-in-your-sql-database"></a>Criar uma tabela de origem de dados na base de dados SQL
1. Abra o SQL Server Management Studio. No **Explorador de Servidores**, clique com p botão direito do rato na base de dados e escolha **Nova Consulta**.

2. Execute o seguinte comando SQL na base de dados SQL para criar uma tabela com o nome `data_source_table` e armazenar o valor de limite de tamaho: 
    
    ```sql
    create table data_source_table
    (
        PersonID int,
        Name varchar(255),
        LastModifytime datetime
    );

    INSERT INTO data_source_table
    (PersonID, Name, LastModifytime)
    VALUES
    (1, 'aaaa','9/1/2017 12:56:00 AM'),
    (2, 'bbbb','9/2/2017 5:23:00 AM'),
    (3, 'cccc','9/3/2017 2:36:00 AM'),
    (4, 'dddd','9/4/2017 3:21:00 AM'),
    (5, 'eeee','9/5/2017 8:06:00 AM');
    ```
    Neste tutorial, vai utilizar LastModifytime como a coluna de limite de tamanho. Os dados no arquivo da origem de dados são apresentados na tabela seguinte:

    ```
    PersonID | Name | LastModifytime
    -------- | ---- | --------------
    1 | aaaa | 2017-09-01 00:56:00.000
    2 | bbbb | 2017-09-02 05:23:00.000
    3 | cccc | 2017-09-03 02:36:00.000
    4 | dddd | 2017-09-04 03:21:00.000
    5 | eeee | 2017-09-05 08:06:00.000
    ```

### <a name="create-another-table-in-your-sql-database-to-store-the-high-watermark-value"></a>Criar outra tabela na base de dados SQL para armazenar o valor de limite superior de tamanho
1. Execute o seguinte comando SQL na base de dados SQL para criar uma tabela com o nome `watermarktable` e armazenar o valor de marca d'água:  
    
    ```sql
    create table watermarktable
    (
    
    TableName varchar(255),
    WatermarkValue datetime,
    );
    ```
2. Defina o valor predefinido do limite superior de tamanho com o nome da tabela do arquivo de dados de origem. Neste tutorial, o nome da tabela é data_source_table.

    ```sql
    INSERT INTO watermarktable
    VALUES ('data_source_table','1/1/2010 12:00:00 AM')    
    ```
3. Reveja os dados na tabela `watermarktable`.
    
    ```sql
    Select * from watermarktable
    ```
    Saída: 

    ```
    TableName  | WatermarkValue
    ----------  | --------------
    data_source_table | 2010-01-01 00:00:00.000
    ```

### <a name="create-a-stored-procedure-in-your-sql-database"></a>Criar um procedimento armazenado na base de dados SQL 

Execute o comando seguinte para criar um procedimento armazenado na base de dados SQL:

```sql
CREATE PROCEDURE usp_write_watermark @LastModifiedtime datetime, @TableName varchar(50)
AS

BEGIN
    
    UPDATE watermarktable
    SET [WatermarkValue] = @LastModifiedtime 
WHERE [TableName] = @TableName
    
END
```

## <a name="create-a-data-factory"></a>Criar uma fábrica de dados
1. Defina uma variável para o nome do grupo de recursos que vai utilizar nos comandos do PowerShell mais tarde. Copie o texto do comando seguinte para o PowerShell, especifique um nome para o [Grupo de recursos do Azure](../azure-resource-manager/resource-group-overview.md) com aspas duplas e execute o comando. Um exemplo é `"adfrg"`. 
   
     ```powershell
    $resourceGroupName = "ADFTutorialResourceGroup";
    ```

    Se o grupo de recursos já existir, é possível que não queira substituí-lo. Atribua outro valor à variável `$resourceGroupName` e execute novamente o comando.

2. Defina uma variável para a localização da fábrica de dados. 

    ```powershell
    $location = "East US"
    ```
3. Para criar o grupo de recursos do Azure, execute o comando abaixo: 

    ```powershell
    New-AzResourceGroup $resourceGroupName $location
    ``` 
    Se o grupo de recursos já existir, é possível que não queira substituí-lo. Atribua outro valor à variável `$resourceGroupName` e execute novamente o comando.

4. Defina uma variável para o nome da fábrica de dados. 

    > [!IMPORTANT]
    >  Atualize o nome da fábrica de dados para que seja globalmente exclusivo. Por exemplo, ADFTutorialFactorySP1127. 

    ```powershell
    $dataFactoryName = "ADFIncCopyTutorialFactory";
    ```
5. Para criar a fábrica de dados, execute o seguinte procedimento **Set-AzDataFactoryV2** cmdlet: 
    
    ```powershell       
    Set-AzDataFactoryV2 -ResourceGroupName $resourceGroupName -Location "East US" -Name $dataFactoryName 
    ```

Tenha em atenção os seguintes pontos:

* O nome da fábrica de dados tem de ser globalmente exclusivo. Se receber o erro seguinte, altere o nome e tente novamente:

    ```
    The specified Data Factory name 'ADFv2QuickStartDataFactory' is already in use. Data Factory names must be globally unique.
    ```

* Para criar instâncias do Data Factory, a conta de utilizador que utiliza para iniciar sessão no Azure tem de ser membro das funções contribuidor ou proprietário ou administrador da subscrição do Azure.
* Para obter uma lista de regiões do Azure em que a fábrica de dados está atualmente disponível, selecione as regiões que lhe interessam, na página seguinte e, em seguida, expanda **Analytics** para localizar **Data Factory**: [Produtos disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/). Os arquivos de dados (Armazenamento, Base de Dados SQL, etc.) e as computações (HDInsight do Azure, etc.) que a fábrica de dados utiliza podem estar noutras regiões.


## <a name="create-linked-services"></a>Criar serviços ligados
Os serviços ligados são criados numa fábrica de dados para ligar os seus arquivos de dados e serviços de computação a essa fábrica de dados. Nesta secção, vai criar serviços ligados à sua conta de Armazenamento e à base de dados SQL. 

### <a name="create-a-storage-linked-service"></a>Criar um serviço ligado ao Armazenamento
1. Crie um ficheiro JSON com o nome AzureStorageLinkedService.json na pasta C:\ADF com o conteúdo seguinte. (Crie a pasta ADF, caso ainda não exista.) Substitua `<accountName>` e `<accountKey>` com o nome e a chave da conta de armazenamento antes de guardar o ficheiro.

    ```json
    {
        "name": "AzureStorageLinkedService",
        "properties": {
            "type": "AzureStorage",
            "typeProperties": {
                "connectionString": {
                    "value": "DefaultEndpointsProtocol=https;AccountName=<accountName>;AccountKey=<accountKey>",
                    "type": "SecureString"
                }
            }
        }
    }
    ```
2. No PowerShell, mude para a pasta ADF.

3. Executar o **Set-AzDataFactoryV2LinkedService** cmdlet para criar o serviço ligado AzureStorageLinkedService. No exemplo seguinte, vai transmitir os valores para os parâmetros *ResourceGroupName* e *DataFactoryName*: 

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

### <a name="create-a-sql-database-linked-service"></a>Criar um serviço ligado à Base de Dados SQL
1. Crie um ficheiro JSON com o nome AzureSQLDatabaseLinkedService.json na pasta C:\ADF com o conteúdo seguinte. (Crie a pasta ADF, caso ainda não exista.) Substitua &lt;server&gt;, &lt;database&gt;, &lt;user id&gt; e &lt;password&gt; pelo nome do servidor, da base de dados, do ID de utilizador e da palavra-passe antes de guardar o ficheiro. 

    ```json
    {
        "name": "AzureSQLDatabaseLinkedService",
        "properties": {
            "type": "AzureSqlDatabase",
            "typeProperties": {
                "connectionString": {
                    "value": "Server = tcp:<server>.database.windows.net,1433;Initial Catalog=<database>; Persist Security Info=False; User ID=<user> ; Password=<password>; MultipleActiveResultSets = False; Encrypt = True; TrustServerCertificate = False; Connection Timeout = 30;",
                    "type": "SecureString"
                }
            }
        }
    }
    ```
2. No PowerShell, mude para a pasta ADF.

3. Executar o **Set-AzDataFactoryV2LinkedService** cmdlet para criar o serviço ligado AzureSQLDatabaseLinkedService. 

    ```powershell
    Set-AzDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureSQLDatabaseLinkedService" -File ".\AzureSQLDatabaseLinkedService.json"
    ```

    Segue-se o resultado do exemplo:

    ```json
    LinkedServiceName : AzureSQLDatabaseLinkedService
    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlDatabaseLinkedService
    ProvisioningState :
    ```

## <a name="create-datasets"></a>Criar conjuntos de dados
Neste passo, vai criar conjuntos de dados para representar os dados de origem e sink. 

### <a name="create-a-source-dataset"></a>Criar um conjunto de dados de origem

1. Crie um ficheiro JSON com o nome SourceDataset.json na mesma pasta com o seguinte conteúdo: 

    ```json
    {
        "name": "SourceDataset",
        "properties": {
            "type": "AzureSqlTable",
            "typeProperties": {
                "tableName": "data_source_table"
            },
            "linkedServiceName": {
                "referenceName": "AzureSQLDatabaseLinkedService",
                "type": "LinkedServiceReference"
            }
        }
    }
   
    ```
    Neste tutorial, vai utilizar o nome de tabela data_source_table. Substitua-o se utilizar uma tabela com um nome diferente.

2. Executar o **Set-AzDataFactoryV2Dataset** cmdlet para criar o conjunto de dados SourceDataset.
    
    ```powershell
    Set-AzDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "SourceDataset" -File ".\SourceDataset.json"
    ```

    Eis a saída de exemplo do cmdlet:
    
    ```json
    DatasetName       : SourceDataset
    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlTableDataset
    ```

### <a name="create-a-sink-dataset"></a>Criar um conjunto de dados de sink

1. Crie um ficheiro JSON com o nome SinkDataset.json na mesma pasta com o seguinte conteúdo: 

    ```json
    {
        "name": "SinkDataset",
        "properties": {
            "type": "AzureBlob",
            "typeProperties": {
                "folderPath": "adftutorial/incrementalcopy",
                "fileName": "@CONCAT('Incremental-', pipeline().RunId, '.txt')", 
                "format": {
                    "type": "TextFormat"
                }
            },
            "linkedServiceName": {
                "referenceName": "AzureStorageLinkedService",
                "type": "LinkedServiceReference"
            }
        }
    }   
    ```

    > [!IMPORTANT]
    > Este fragmento parte do princípio de que tem um contentor de blobs denominado adftutorial no armazenamento de blobs. Crie o contentor se ainda não existir ou defina-o como o nome de um contentor existente. A pasta de saída `incrementalcopy` é criada automaticamente se não existir no contentor. Neste tutorial, o nome de ficheiro é gerado dinamicamente através da expressão `@CONCAT('Incremental-', pipeline().RunId, '.txt')`.

2. Executar o **Set-AzDataFactoryV2Dataset** cmdlet para criar o conjunto de dados SinkDataset.
    
    ```powershell
    Set-AzDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "SinkDataset" -File ".\SinkDataset.json"
    ```

    Eis a saída de exemplo do cmdlet:
    
    ```json
    DatasetName       : SinkDataset
    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureBlobDataset    
    ```

## <a name="create-a-dataset-for-a-watermark"></a>Criar um conjunto de dados para um limite de tamanho
Neste passo, vai criar um conjunto de dados para armazenar um valor de limite superior de tamanho. 

1. Crie um ficheiro JSON com o nome WatermarkDataset.json na mesma pasta com o seguinte conteúdo: 

    ```json
    {
        "name": " WatermarkDataset ",
        "properties": {
            "type": "AzureSqlTable",
            "typeProperties": {
                "tableName": "watermarktable"
            },
            "linkedServiceName": {
                "referenceName": "AzureSQLDatabaseLinkedService",
                "type": "LinkedServiceReference"
            }
        }
    }    
    ```
2.  Executar o **Set-AzDataFactoryV2Dataset** cmdlet para criar o conjunto de dados WatermarkDataset.
    
    ```powershell
    Set-AzDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "WatermarkDataset" -File ".\WatermarkDataset.json"
    ```

    Eis a saída de exemplo do cmdlet:
    
    ```json
    DatasetName       : WatermarkDataset
    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlTableDataset    
    ```

## <a name="create-a-pipeline"></a>Criar um pipeline
Neste tutorial, vai criar um pipeline com duas atividades Lookup uma atividade Copy e uma atividade StoredProcedure encadeadas num pipeline. 


1. Crie um ficheiro JSON IncrementalCopyPipeline.json na mesma pasta com o seguinte conteúdo: 

    ```json
    {
        "name": "IncrementalCopyPipeline",
        "properties": {
            "activities": [
                {
                    "name": "LookupOldWaterMarkActivity",
                    "type": "Lookup",
                    "typeProperties": {
                        "source": {
                        "type": "SqlSource",
                        "sqlReaderQuery": "select * from watermarktable"
                        },
    
                        "dataset": {
                        "referenceName": "WatermarkDataset",
                        "type": "DatasetReference"
                        }
                    }
                },
                {
                    "name": "LookupNewWaterMarkActivity",
                    "type": "Lookup",
                    "typeProperties": {
                        "source": {
                            "type": "SqlSource",
                            "sqlReaderQuery": "select MAX(LastModifytime) as NewWatermarkvalue from data_source_table"
                        },
    
                        "dataset": {
                        "referenceName": "SourceDataset",
                        "type": "DatasetReference"
                        }
                    }
                },
                
                {
                    "name": "IncrementalCopyActivity",
                    "type": "Copy",
                    "typeProperties": {
                        "source": {
                            "type": "SqlSource",
                            "sqlReaderQuery": "select * from data_source_table where LastModifytime > '@{activity('LookupOldWaterMarkActivity').output.firstRow.WatermarkValue}' and LastModifytime <= '@{activity('LookupNewWaterMarkActivity').output.firstRow.NewWatermarkvalue}'"
                        },
                        "sink": {
                            "type": "BlobSink"
                        }
                    },
                    "dependsOn": [
                        {
                            "activity": "LookupNewWaterMarkActivity",
                            "dependencyConditions": [
                                "Succeeded"
                            ]
                        },
                        {
                            "activity": "LookupOldWaterMarkActivity",
                            "dependencyConditions": [
                                "Succeeded"
                            ]
                        }
                    ],
    
                    "inputs": [
                        {
                            "referenceName": "SourceDataset",
                            "type": "DatasetReference"
                        }
                    ],
                    "outputs": [
                        {
                            "referenceName": "SinkDataset",
                            "type": "DatasetReference"
                        }
                    ]
                },
    
                {
                    "name": "StoredProceduretoWriteWatermarkActivity",
                    "type": "SqlServerStoredProcedure",
                    "typeProperties": {
    
                        "storedProcedureName": "usp_write_watermark",
                        "storedProcedureParameters": {
                            "LastModifiedtime": {"value": "@{activity('LookupNewWaterMarkActivity').output.firstRow.NewWatermarkvalue}", "type": "datetime" },
                            "TableName":  { "value":"@{activity('LookupOldWaterMarkActivity').output.firstRow.TableName}", "type":"String"}
                        }
                    },
    
                    "linkedServiceName": {
                        "referenceName": "AzureSQLDatabaseLinkedService",
                        "type": "LinkedServiceReference"
                    },
    
                    "dependsOn": [
                        {
                            "activity": "IncrementalCopyActivity",
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
    

2. Executar o **Set-AzDataFactoryV2Pipeline** cmdlet para criar o pipeline IncrementalCopyPipeline.
    
   ```powershell
   Set-AzDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "IncrementalCopyPipeline" -File ".\IncrementalCopyPipeline.json"
   ``` 

   Segue-se o resultado do exemplo: 

   ```json
    PipelineName      : IncrementalCopyPipeline
    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    Activities        : {LookupOldWaterMarkActivity, LookupNewWaterMarkActivity, IncrementalCopyActivity, StoredProceduretoWriteWatermarkActivity}
    Parameters        :
   ```
 
## <a name="run-the-pipeline"></a>Executar o pipeline

1. Execute o pipeline IncrementalCopyPipeline com o **Invoke-AzDataFactoryV2Pipeline** cmdlet. Substitua os marcadores de posição pelos nomes do seu grupo de recursos e da sua fábrica de dados.

    ```powershell
    $RunId = Invoke-AzDataFactoryV2Pipeline -PipelineName "IncrementalCopyPipeline" -ResourceGroupName $resourceGroupName -dataFactoryName $dataFactoryName
    ``` 
2. Verifique o estado do pipeline, executando o **Get-AzDataFactoryV2ActivityRun** cmdlet até ver todas as atividades executadas com êxito. Substitua os marcadores de posição pela sua hora apropriada para os parâmetros *RunStartedAfter* e *RunStartedBefore*. Neste tutorial, vai utilizar *-RunStartedAfter "2017/09/14"* e *-RunStartedBefore "2017/09/15"*.

    ```powershell
    Get-AzDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId $RunId -RunStartedAfter "<start time>" -RunStartedBefore "<end time>"
    ```

    Segue-se o resultado do exemplo:
 
    ```json
    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    ActivityName      : LookupNewWaterMarkActivity
    PipelineRunId     : d4bf3ce2-5d60-43f3-9318-923155f61037
    PipelineName      : IncrementalCopyPipeline
    Input             : {source, dataset}
    Output            : {NewWatermarkvalue}
    LinkedServiceName :
    ActivityRunStart  : 9/14/2017 7:42:42 AM
    ActivityRunEnd    : 9/14/2017 7:42:50 AM
    DurationInMs      : 7777
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}
    
    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    ActivityName      : LookupOldWaterMarkActivity
    PipelineRunId     : d4bf3ce2-5d60-43f3-9318-923155f61037
    PipelineName      : IncrementalCopyPipeline
    Input             : {source, dataset}
    Output            : {TableName, WatermarkValue}
    LinkedServiceName :
    ActivityRunStart  : 9/14/2017 7:42:42 AM
    ActivityRunEnd    : 9/14/2017 7:43:07 AM
    DurationInMs      : 25437
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}
    
    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    ActivityName      : IncrementalCopyActivity
    PipelineRunId     : d4bf3ce2-5d60-43f3-9318-923155f61037
    PipelineName      : IncrementalCopyPipeline
    Input             : {source, sink}
    Output            : {dataRead, dataWritten, rowsCopied, copyDuration...}
    LinkedServiceName :
    ActivityRunStart  : 9/14/2017 7:43:10 AM
    ActivityRunEnd    : 9/14/2017 7:43:29 AM
    DurationInMs      : 19769
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}
    
    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    ActivityName      : StoredProceduretoWriteWatermarkActivity
    PipelineRunId     : d4bf3ce2-5d60-43f3-9318-923155f61037
    PipelineName      : IncrementalCopyPipeline
    Input             : {storedProcedureName, storedProcedureParameters}
    Output            : {}
    LinkedServiceName :
    ActivityRunStart  : 9/14/2017 7:43:32 AM
    ActivityRunEnd    : 9/14/2017 7:43:47 AM
    DurationInMs      : 14467
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}

    ```

## <a name="review-the-results"></a>Rever os resultados

1. No armazenamento de blobs (arquivo sink), vê que os dados foram copiados para o ficheiro definido em SinkDataset. Neste tutorial, o nome de ficheiro é `Incremental- d4bf3ce2-5d60-43f3-9318-923155f61037.txt`. Ao abrir o ficheiro, poderá ver que os registos no ficheiro são iguais aos dados na base de dados SQL.

    ```
    1,aaaa,2017-09-01 00:56:00.0000000
    2,bbbb,2017-09-02 05:23:00.0000000
    3,cccc,2017-09-03 02:36:00.0000000
    4,dddd,2017-09-04 03:21:00.0000000
    5,eeee,2017-09-05 08:06:00.0000000
    ``` 
2. Verifique o valor mais recente do `watermarktable`. Verá que o valor de limite de tamanho foi atualizado.

    ```sql
    Select * from watermarktable
    ```
    
    Segue-se o resultado do exemplo:
 
    TableName | WatermarkValue
    --------- | --------------
    data_source_table | 2017-09-05  8:06:00.000

### <a name="insert-data-into-the-data-source-store-to-verify-delta-data-loading"></a>Inserir dados no arquivo de dados de origem para verificar o carregamento de dados delta

1. Insira novos dados na base de dados SQL (arquivo de dados de origem).

    ```sql
    INSERT INTO data_source_table
    VALUES (6, 'newdata','9/6/2017 2:23:00 AM')
    
    INSERT INTO data_source_table
    VALUES (7, 'newdata','9/7/2017 9:01:00 AM')
    ``` 

    Os dados atualizados na base de dados SQL são:

    ```
    PersonID | Name | LastModifytime
    -------- | ---- | --------------
    1 | aaaa | 2017-09-01 00:56:00.000
    2 | bbbb | 2017-09-02 05:23:00.000
    3 | cccc | 2017-09-03 02:36:00.000
    4 | dddd | 2017-09-04 03:21:00.000
    5 | eeee | 2017-09-05 08:06:00.000
    6 | newdata | 2017-09-06 02:23:00.000
    7 | newdata | 2017-09-07 09:01:00.000
    ```
2. Execute o pipeline IncrementalCopyPipeline novamente com o **Invoke-AzDataFactoryV2Pipeline** cmdlet. Substitua os marcadores de posição pelos nomes do seu grupo de recursos e da sua fábrica de dados.

    ```powershell
    $RunId = Invoke-AzDataFactoryV2Pipeline -PipelineName "IncrementalCopyPipeline" -ResourceGroupName $resourceGroupName -dataFactoryName $dataFactoryName
    ```
3. Verifique o estado do pipeline, executando o **Get-AzDataFactoryV2ActivityRun** cmdlet até ver todas as atividades executadas com êxito. Substitua os marcadores de posição pela sua hora apropriada para os parâmetros *RunStartedAfter* e *RunStartedBefore*. Neste tutorial, vai utilizar *-RunStartedAfter "2017/09/14"* e *-RunStartedBefore "2017/09/15"*.

    ```powershell
    Get-AzDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId $RunId -RunStartedAfter "<start time>" -RunStartedBefore "<end time>"
    ```

    Segue-se o resultado do exemplo:
 
    ```json
    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    ActivityName      : LookupNewWaterMarkActivity
    PipelineRunId     : 2fc90ab8-d42c-4583-aa64-755dba9925d7
    PipelineName      : IncrementalCopyPipeline
    Input             : {source, dataset}
    Output            : {NewWatermarkvalue}
    LinkedServiceName :
    ActivityRunStart  : 9/14/2017 8:52:26 AM
    ActivityRunEnd    : 9/14/2017 8:52:58 AM
    DurationInMs      : 31758
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}
    
    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    ActivityName      : LookupOldWaterMarkActivity
    PipelineRunId     : 2fc90ab8-d42c-4583-aa64-755dba9925d7
    PipelineName      : IncrementalCopyPipeline
    Input             : {source, dataset}
    Output            : {TableName, WatermarkValue}
    LinkedServiceName :
    ActivityRunStart  : 9/14/2017 8:52:26 AM
    ActivityRunEnd    : 9/14/2017 8:52:52 AM
    DurationInMs      : 25497
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}
    
    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    ActivityName      : IncrementalCopyActivity
    PipelineRunId     : 2fc90ab8-d42c-4583-aa64-755dba9925d7
    PipelineName      : IncrementalCopyPipeline
    Input             : {source, sink}
    Output            : {dataRead, dataWritten, rowsCopied, copyDuration...}
    LinkedServiceName :
    ActivityRunStart  : 9/14/2017 8:53:00 AM
    ActivityRunEnd    : 9/14/2017 8:53:20 AM
    DurationInMs      : 20194
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}
    
    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    ActivityName      : StoredProceduretoWriteWatermarkActivity
    PipelineRunId     : 2fc90ab8-d42c-4583-aa64-755dba9925d7
    PipelineName      : IncrementalCopyPipeline
    Input             : {storedProcedureName, storedProcedureParameters}
    Output            : {}
    LinkedServiceName :
    ActivityRunStart  : 9/14/2017 8:53:23 AM
    ActivityRunEnd    : 9/14/2017 8:53:41 AM
    DurationInMs      : 18502
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}

    ```
4. No armazenamento de blobs, verá que outro ficheiro foi criado. Neste tutorial, o novo nome de ficheiro é `Incremental-2fc90ab8-d42c-4583-aa64-755dba9925d7.txt`. Abra esse ficheiro e verá duas linhas de registos no mesmo.

5. Verifique o valor mais recente do `watermarktable`. Verá que o valor de marca d’água foi atualizado.

    ```sql
    Select * from watermarktable
    ```
    saída de exemplo: 
    
    TableName | WatermarkValue
    --------- | ---------------
    data_source_table | 2017-09-07 09:01:00.000

     
## <a name="next-steps"></a>Passos seguintes
Neste tutorial, executou os passos seguintes: 

> [!div class="checklist"]
> * Preparar o arquivo de dados para armazenar o valor de limite de tamanho. 
> * Criar uma fábrica de dados.
> * Criar serviços ligados. 
> * Crie conjuntos de dados de origem, de sink e de limite de tamanho.
> * Criar um pipeline.
> * Executar o pipeline.
> * Monitorizar a execução do pipeline. 

Neste tutorial, o pipeline copiou dados a partir de uma única tabela numa base de dados SQL para um armazenamento de Blobs. Avance para o tutorial seguinte para saber como copiar dados de várias tabelas numa base de dados do SQL Server no local para uma base de dados SQL. 

> [!div class="nextstepaction"]
>[Carregar dados de forma incremental a partir de várias tabelas no SQL Server para a Base de Dados SQL do Azure](tutorial-incremental-copy-multiple-tables-powershell.md)



