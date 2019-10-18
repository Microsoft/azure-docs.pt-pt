---
title: Copiar várias tabelas de forma incremental com o Azure Data Factory | Microsoft Docs
description: Neste tutorial, vai criar um pipeline do Azure Data Factory, que copia dados delta de forma incremental de várias tabelas numa base de dados do SQL Server local para uma base de dados SQL do Azure.
services: data-factory
documentationcenter: ''
author: dearandyxu
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: tutorial
ms.date: 01/22/2018
ms.author: yexu
ms.openlocfilehash: b7de8b164fcd818fba1f999ea7b67f11de646ccd
ms.sourcegitcommit: 6eecb9a71f8d69851bc962e2751971fccf29557f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/17/2019
ms.locfileid: "72533197"
---
# <a name="incrementally-load-data-from-multiple-tables-in-sql-server-to-an-azure-sql-database"></a>Carregar dados de forma incremental a partir de várias tabelas no SQL Server para uma base de dados SQL do Azure
Neste tutorial, vai criar um pipeline do Azure Data Factory que carrega dados delta a partir de várias tabelas no SQL Server local para uma base de dados SQL do Azure.    

Vai executar os seguintes passos neste tutorial:

> [!div class="checklist"]
> * Prepare os arquivos de dados de origem e de destino.
> * Criar uma fábrica de dados.
> * Criar um integration runtime autoalojado.
> * Instalou o integration runtime. 
> * Criar serviços ligados. 
> * Crie conjuntos de dados de origem, de sink e de limite de tamanho.
> * Criar, executar e monitorizar um pipeline.
> * Reveja os resultados.
> * Adicionou ou atualizou os dados nas tabelas de origem.
> * Voltou a executar e a monitorizar o pipeline.
> * Rever os resultados finais.

## <a name="overview"></a>Visão geral
Eis os passos importantes para criar esta solução: 

1. **Selecionar a coluna de limite de tamanho**.
    Selecione uma coluna por cada tabela no arquivo de dados de origem, que pode ser utilizada para identificar os registos novos ou atualizados para cada execução. Normalmente, os dados nesta coluna selecionada (por exemplo, last_modify_time ou ID) continuam a aumentar quando as linhas são criadas ou atualizadas. O valor máximo nesta coluna é utilizado como limite de tamanho.

1. **Preparar um arquivo de dados para armazenar o valor de limite de tamanho**.   
    Neste tutorial, vai armazenar o valor de limite superior numa base de dados SQL.

1. **Criar um pipeline com as seguintes atividades:** 
    
    a. Criar uma atividade ForEach que itera através de uma lista de nomes de tabelas de origem que é transmitida como um parâmetro para o pipeline. Para cada tabela de origem, este invoca as seguintes atividades para efetuar o carregamento de diferenças para essa tabela.

    b. Criar duas atividades de pesquisa. Utilize a primeira atividade Lookup para obter o último valor de limite de tamanho. Utilize a segunda para obter o valor de limite de tamanho novo. Estes valores de limite de tamanho são transmitidos para a atividade Copy.

    c. Criar uma atividade Copy que copia linhas do arquivo de dados de origem com o valor da coluna de limite de tamanho superior ao valor de limite de tamanho antigo e inferior ao valor novo. Em seguida, copia os dados delta do arquivo de dados de origem para o armazenamento de Blobs do Azure como um ficheiro novo.

    d. Crie uma atividade StoredProcedure, que atualiza o valor de marca d'água do pipeline que vai ser executado da próxima vez. 

    Eis o diagrama da solução de alto nível: 

    ![Carregar dados de forma incremental](media/tutorial-incremental-copy-multiple-tables-powershell/high-level-solution-diagram.png)


Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos
* **SQL Server**. Neste tutorial, vai utilizar uma base de dados do SQL Server no local como o arquivo de dados de origem. 
* **Base de Dados SQL do Azure**. Vai ulizar uma base de dados SQL como o arquivo de dados de sink. Se não tiver uma base de dados SQL, veja [Criar uma base de dados SQL do Azure](../sql-database/sql-database-get-started-portal.md) para seguir os passos para criar uma. 

### <a name="create-source-tables-in-your-sql-server-database"></a>Criar tabelas de origem na base de dados do SQL Server

1. Abra o SQL Server Management Studio e ligue-se à base de dados do SQL Server no local.

1. No **Explorador de Servidores**, clique com botão direito do rato na base de dados e escolha **Nova Consulta**.

1. Execute o seguinte comando SQL na base de dados para criar tabelas com o nome `customer_table` e `project_table`:

    ```sql
    create table customer_table
    (
        PersonID int,
        Name varchar(255),
        LastModifytime datetime
    );
    
    create table project_table
    (
        Project varchar(255),
        Creationtime datetime
    );
        
    INSERT INTO customer_table
    (PersonID, Name, LastModifytime)
    VALUES
    (1, 'John','9/1/2017 12:56:00 AM'),
    (2, 'Mike','9/2/2017 5:23:00 AM'),
    (3, 'Alice','9/3/2017 2:36:00 AM'),
    (4, 'Andy','9/4/2017 3:21:00 AM'),
    (5, 'Anny','9/5/2017 8:06:00 AM');
    
    INSERT INTO project_table
    (Project, Creationtime)
    VALUES
    ('project1','1/1/2015 0:00:00 AM'),
    ('project2','2/2/2016 1:23:00 AM'),
    ('project3','3/4/2017 5:16:00 AM');
    
    ```

### <a name="create-destination-tables-in-your-azure-sql-database"></a>Criar tabelas de destino na base de dados SQL do Azure
1. Abra o SQL Server Management Studio e ligue-se à base de dados do SQL Server.

1. No **Explorador de Servidores**, clique com botão direito do rato na base de dados e escolha **Nova Consulta**.

1. Execute o comando SQL seguinte na base de dados SQL para criar tabelas com o nome `customer_table` e `project_table`:  
    
    ```sql
    create table customer_table
    (
        PersonID int,
        Name varchar(255),
        LastModifytime datetime
    );
    
    create table project_table
    (
        Project varchar(255),
        Creationtime datetime
    );

    ```

### <a name="create-another-table-in-the-azure-sql-database-to-store-the-high-watermark-value"></a>Criar outra tabela na base de dados SQL do Azure para armazenar o valor de limite superior de tamanho
1. Execute o seguinte comando SQL na base de dados SQL para criar uma tabela com o nome `watermarktable` e armazenar o valor de marca d'água: 
    
    ```sql
    create table watermarktable
    (
    
        TableName varchar(255),
        WatermarkValue datetime,
    );
    ```
1. Inserir valores de marca d'água iniciais para ambas as tabelas de origem na tabela de marca d'água.

    ```sql

    INSERT INTO watermarktable
    VALUES 
    ('customer_table','1/1/2010 12:00:00 AM'),
    ('project_table','1/1/2010 12:00:00 AM');
    
    ```

### <a name="create-a-stored-procedure-in-the-azure-sql-database"></a>Criar um procedimento armazenado na base de dados SQL do Azure 

Execute o comando seguinte para criar um procedimento armazenado na base de dados SQL. Este procedimento armazenado atualiza o valor de limite de tamanho após cada execução de pipeline. 

```sql
CREATE PROCEDURE usp_write_watermark @LastModifiedtime datetime, @TableName varchar(50)
AS

BEGIN

    UPDATE watermarktable
    SET [WatermarkValue] = @LastModifiedtime 
WHERE [TableName] = @TableName

END

```

### <a name="create-data-types-and-additional-stored-procedures-in-the-azure-sql-database"></a>Criar tipos de dados e procedimentos armazenados adicionais na base de dados SQL do Azure
Execute a consulta seguinte para criar dois procedimentos armazenados e dois tipos de dados de dois na sua base de dados SQL. São utilizados para intercalar os dados das tabelas de origem nas tabelas de destino. 

Para facilitar o início da jornada com o, usamos diretamente esses procedimentos armazenados passando os dados Delta por meio de uma variável de tabela e, em seguida, mesclando-os no repositório de destino. Tenha cuidado para que ele não esteja esperando um número "grande" de linhas Delta (mais de 100) para ser armazenado na variável de tabela.  

Se você precisar mesclar um grande número de linhas Delta no repositório de destino, sugerimos que você use a atividade de cópia para copiar todos os dados Delta para uma tabela temporária de "preparo" no armazenamento de destino primeiro e, em seguida, criar seu próprio procedimento armazenado sem usar a tabela acento grave é possível mesclá-los da tabela "preparo" para a tabela "final". 


```sql
CREATE TYPE DataTypeforCustomerTable AS TABLE(
    PersonID int,
    Name varchar(255),
    LastModifytime datetime
);

GO

CREATE PROCEDURE usp_upsert_customer_table @customer_table DataTypeforCustomerTable READONLY
AS

BEGIN
  MERGE customer_table AS target
  USING @customer_table AS source
  ON (target.PersonID = source.PersonID)
  WHEN MATCHED THEN
      UPDATE SET Name = source.Name,LastModifytime = source.LastModifytime
  WHEN NOT MATCHED THEN
      INSERT (PersonID, Name, LastModifytime)
      VALUES (source.PersonID, source.Name, source.LastModifytime);
END

GO

CREATE TYPE DataTypeforProjectTable AS TABLE(
    Project varchar(255),
    Creationtime datetime
);

GO

CREATE PROCEDURE usp_upsert_project_table @project_table DataTypeforProjectTable READONLY
AS

BEGIN
  MERGE project_table AS target
  USING @project_table AS source
  ON (target.Project = source.Project)
  WHEN MATCHED THEN
      UPDATE SET Creationtime = source.Creationtime
  WHEN NOT MATCHED THEN
      INSERT (Project, Creationtime)
      VALUES (source.Project, source.Creationtime);
END

```

### <a name="azure-powershell"></a>Azure PowerShell
Siga as instruções em [Instalar e Configurar o Azure PowerShell](/powershell/azure/azurerm/install-azurerm-ps) para instalar os módulos mais recentes do Azure PowerShell.

## <a name="create-a-data-factory"></a>Criar uma fábrica de dados
1. Defina uma variável para o nome do grupo de recursos que vai utilizar nos comandos do PowerShell mais tarde. Copie o texto do comando seguinte para o PowerShell, especifique um nome para o [Grupo de recursos do Azure](../azure-resource-manager/resource-group-overview.md) com aspas duplas e execute o comando. Um exemplo é `"adfrg"`. 
   
    ```powershell
    $resourceGroupName = "ADFTutorialResourceGroup";
    ```

    Se o grupo de recursos já existir, é possível que não queira substituí-lo. Atribua outro valor à variável `$resourceGroupName` e execute novamente o comando.

1. Defina uma variável para a localização da fábrica de dados. 

    ```powershell
    $location = "East US"
    ```
1. Para criar o grupo de recursos do Azure, execute o comando abaixo: 

    ```powershell
    New-AzResourceGroup $resourceGroupName $location
    ``` 
    Se o grupo de recursos já existir, é possível que não queira substituí-lo. Atribua outro valor à variável `$resourceGroupName` e execute novamente o comando.

1. Defina uma variável para o nome da fábrica de dados. 

    > [!IMPORTANT]
    >  Atualize o nome da fábrica de dados, para que seja globalmente exclusivo. Um exemplo é ADFIncMultiCopyTutorialFactorySP1127. 

    ```powershell
    $dataFactoryName = "ADFIncMultiCopyTutorialFactory";
    ```
1. Para criar o data factory, execute o seguinte cmdlet **set-AzDataFactoryV2** : 
    
    ```powershell       
    Set-AzDataFactoryV2 -ResourceGroupName $resourceGroupName -Location $location -Name $dataFactoryName 
    ```

Tenha em atenção os seguintes pontos:

* O nome da fábrica de dados tem de ser globalmente exclusivo. Se receber o erro seguinte, altere o nome e tente novamente:

    ```
    The specified Data Factory name 'ADFIncMultiCopyTutorialFactory' is already in use. Data Factory names must be globally unique.
    ```
* Para criar instâncias do Data Factory, a conta de utilizador que utiliza para iniciar sessão no Azure tem de ser membro das funções contribuidor ou proprietário ou administrador da subscrição do Azure.
* Para obter uma lista de regiões do Azure em que o Data Factory está atualmente disponível, selecione as regiões que lhe interessam na página seguinte e, em seguida, expanda **Analytics** para localizar **Data Factory**: [Produtos disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/). Os arquivos de dados (Armazenamento do Azure, Base de Dados SQL do Azure, etc.) e as computações (HDInsight, etc.) que a fábrica de dados utiliza podem estar noutras regiões.

[!INCLUDE [data-factory-create-install-integration-runtime](../../includes/data-factory-create-install-integration-runtime.md)]


## <a name="create-linked-services"></a>Criar serviços ligados
Os serviços ligados são criados numa fábrica de dados para ligar os seus arquivos de dados e serviços de computação a essa fábrica de dados. Nesta secção, vai criar serviços ligados à base de dados SQL do Azure local e à base de dados SQL do Azure. 

### <a name="create-the-sql-server-linked-service"></a>Criar o serviço ligado do SQL Server
Neste passo, vai ligar a base de dados do SQL Server no local à fábrica de dados.

1. Crie um arquivo JSON chamado **SqlServerLinkedService. JSON** na pasta C:\ADFTutorials\IncCopyMultiTableTutorial com o conteúdo a seguir. Selecione a secção certa com base na autenticação que utiliza para se ligar ao SQL Server. Crie as pastas locais, caso ainda não existam. 

    > [!IMPORTANT]
    > Selecione a secção certa com base na autenticação que utiliza para se ligar ao SQL Server.

    Se utilizar a autenticação do SQL, copie a seguinte definição JSON:

    ```json
    {  
        "name":"SqlServerLinkedService",
        "properties":{  
            "annotations":[  
    
            ],
            "type":"SqlServer",
            "typeProperties":{  
                "connectionString":"integrated security=False;data source=<servername>;initial catalog=<database name>;user id=<username>;Password=<password>"
            },
            "connectVia":{  
                "referenceName":"<integration runtime name>",
                "type":"IntegrationRuntimeReference"
            }
        }
    } 
   ```    
    Se utilizar a autenticação Windows, copie a seguinte definição JSON:

    ```json
    {  
        "name":"SqlServerLinkedService",
        "properties":{  
            "annotations":[  
    
            ],
            "type":"SqlServer",
            "typeProperties":{  
                "connectionString":"integrated security=True;data source=<servername>;initial catalog=<database name>",
                "userName":"<username> or <domain>\\<username>",
                "password":{  
                    "type":"SecureString",
                    "value":"<password>"
                }
            },
            "connectVia":{  
                "referenceName":"<integration runtime name>",
                "type":"IntegrationRuntimeReference"
            }
        }
    }
    ```
    > [!IMPORTANT]
    > - Selecione a secção certa com base na autenticação que utiliza para se ligar ao SQL Server.
    > - Substitua &lt;integration runtime name> pelo nome do integration runtime.
    > - Antes de guardar o ficheiro, substitua &lt;servername>, &lt;databasename>, &lt;username> e &lt;password> pelos valores da sua base de dados do SQL Server.
    > - Se precisar de utilizar um caráter de barra invertida (`\`) no nome da sua conta de utilizador ou no nome do seu servidor, utilize o caráter de escape (`\`). Um exemplo é `mydomain\\myuser`.

1. No PowerShell, execute o cmdlet a seguir para alternar para a pasta C:\ADFTutorials\IncCopyMultiTableTutorial.

    ```powershell
    Set-Location 'C:\ADFTutorials\IncCopyMultiTableTutorial'
    ```

1. Execute o cmdlet **set-AzDataFactoryV2LinkedService** para criar o serviço vinculado AzureStorageLinkedService. No exemplo seguinte, vai transmitir os valores para os parâmetros *ResourceGroupName* e *DataFactoryName*: 

    ```powershell
    Set-AzDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "SqlServerLinkedService" -File ".\SqlServerLinkedService.json"
    ```

    Segue-se o resultado do exemplo:

    ```json
    LinkedServiceName : SqlServerLinkedService
    ResourceGroupName : <ResourceGroupName>
    DataFactoryName   : <DataFactoryName>
    Properties        : Microsoft.Azure.Management.DataFactory.Models.SqlServerLinkedService
    ```

### <a name="create-the-sql-database-linked-service"></a>Criar o serviço ligado da base de dados SQL
1. Crie um arquivo JSON chamado **AzureSQLDatabaseLinkedService. JSON** na pasta C:\ADFTutorials\IncCopyMultiTableTutorial com o conteúdo a seguir. (Crie a pasta ADF se ela ainda não existir.) Substitua &lt;servername &gt;, &lt;database Name &gt;, &lt;user Name &gt; e &lt;password &gt; pelo nome do seu banco de dados SQL Server, nome do seu banco de dados, nome de usuário e senha antes de salvar o arquivo. 

    ```json
    {  
        "name":"AzureSQLDatabaseLinkedService",
        "properties":{  
            "annotations":[  
    
            ],
            "type":"AzureSqlDatabase",
            "typeProperties":{  
                "connectionString":"integrated security=False;encrypt=True;connection timeout=30;data source=<servername>.database.windows.net;initial catalog=<database name>;user id=<user name>;Password=<password>;"
            }
        }
    }
    ```
1. No PowerShell, execute o cmdlet **set-AzDataFactoryV2LinkedService** para criar o serviço vinculado AzureSQLDatabaseLinkedService. 

    ```powershell
    Set-AzDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureSQLDatabaseLinkedService" -File ".\AzureSQLDatabaseLinkedService.json"
    ```

    Segue-se o resultado do exemplo:

    ```json
    LinkedServiceName : AzureSQLDatabaseLinkedService
    ResourceGroupName : <ResourceGroupName>
    DataFactoryName   : <DataFactoryName>
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlDatabaseLinkedService
    ```

## <a name="create-datasets"></a>Criar conjuntos de dados
Neste passo, vai criar conjuntos de dados para representar a origem de dados, o destino de dados e o local para armazenar o limite de tamanho.

### <a name="create-a-source-dataset"></a>Criar um conjunto de dados de origem

1. Crie um ficheiro JSON com o nome **SourceDataset.json** na mesma pasta com o seguinte conteúdo: 

    ```json
    {  
        "name":"SourceDataset",
        "properties":{  
            "linkedServiceName":{  
                "referenceName":"SqlServerLinkedService",
                "type":"LinkedServiceReference"
            },
            "annotations":[  
    
            ],
            "type":"SqlServerTable",
            "schema":[  
    
            ]
        }
    }
   
    ```

    A atividade Cópia no pipeline utiliza uma consulta SQL para carregar os dados em vez de carregar a tabela inteira.

1. Execute o cmdlet **set-AzDataFactoryV2Dataset** para criar o conjunto de SourceDataset.
    
    ```powershell
    Set-AzDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "SourceDataset" -File ".\SourceDataset.json"
    ```

    Eis a saída de exemplo do cmdlet:
    
    ```json
    DatasetName       : SourceDataset
    ResourceGroupName : <ResourceGroupName>
    DataFactoryName   : <DataFactoryName>
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.SqlServerTableDataset
    ```

### <a name="create-a-sink-dataset"></a>Criar um conjunto de dados de sink

1. Crie um arquivo JSON chamado **SinkDataset. JSON** na mesma pasta com o conteúdo a seguir. O elemento tableName é definido pelo pipeline dinamicamente durante a execução. A atividade ForEach no pipeline itera através de uma lista de nomes de tabelas e transmite o nome da tabela para este conjunto de dados em cada iteração. 

    ```json
    {  
        "name":"SinkDataset",
        "properties":{  
            "linkedServiceName":{  
                "referenceName":"AzureSQLDatabaseLinkedService",
                "type":"LinkedServiceReference"
            },
            "parameters":{  
                "SinkTableName":{  
                    "type":"String"
                }
            },
            "annotations":[  
    
            ],
            "type":"AzureSqlTable",
            "typeProperties":{  
                "tableName":{  
                    "value":"@dataset().SinkTableName",
                    "type":"Expression"
                }
            }
        }
    }
    ```

1. Execute o cmdlet **set-AzDataFactoryV2Dataset** para criar o conjunto de SinkDataset.
    
    ```powershell
    Set-AzDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "SinkDataset" -File ".\SinkDataset.json"
    ```

    Eis a saída de exemplo do cmdlet:
    
    ```json
    DatasetName       : SinkDataset
    ResourceGroupName : <ResourceGroupName>
    DataFactoryName   : <DataFactoryName>
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlTableDataset
    ```

### <a name="create-a-dataset-for-a-watermark"></a>Criar um conjunto de dados para um limite de tamanho
Neste passo, vai criar um conjunto de dados para armazenar um valor de limite superior de tamanho. 

1. Crie um arquivo JSON chamado **WatermarkDataset. JSON** na mesma pasta com o seguinte conteúdo: 

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
1. Execute o cmdlet **set-AzDataFactoryV2Dataset** para criar o conjunto de WatermarkDataset.
    
    ```powershell
    Set-AzDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "WatermarkDataset" -File ".\WatermarkDataset.json"
    ```

    Eis a saída de exemplo do cmdlet:
    
    ```json
    DatasetName       : WatermarkDataset
    ResourceGroupName : <ResourceGroupName>
    DataFactoryName   : <DataFactoryName>
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlTableDataset    
    ```

## <a name="create-a-pipeline"></a>Criar um pipeline
O pipeline aceita uma lista de nomes de tabela como parâmetro. A **atividade ForEach** itera na lista de nomes de tabela e executa as seguintes operações: 

1. Use a **atividade de pesquisa** para recuperar o valor antigo da marca d' água (o valor inicial ou aquele que foi usado na última iteração).

1. Use a **atividade de pesquisa** para recuperar o novo valor de marca d' água (o valor máximo da coluna de marca-d ' água na tabela de origem).

1. Use a **atividade de cópia** para copiar dados entre esses dois valores de marca d' água do banco de dados de origem para o banco de dados de destino.

1. Use a **atividade StoredProcedure** para atualizar o valor antigo da marca d' água a ser usado na primeira etapa da próxima iteração. 

### <a name="create-the-pipeline"></a>Criar o pipeline
1. Crie um arquivo JSON chamado **IncrementalCopyPipeline. JSON** na mesma pasta com o seguinte conteúdo: 

    ```json
    {  
        "name":"IncrementalCopyPipeline",
        "properties":{  
            "activities":[  
                {  
                    "name":"IterateSQLTables",
                    "type":"ForEach",
                    "dependsOn":[  
    
                    ],
                    "userProperties":[  
    
                    ],
                    "typeProperties":{  
                        "items":{  
                            "value":"@pipeline().parameters.tableList",
                            "type":"Expression"
                        },
                        "isSequential":false,
                        "activities":[  
                            {  
                                "name":"LookupOldWaterMarkActivity",
                                "type":"Lookup",
                                "dependsOn":[  
    
                                ],
                                "policy":{  
                                    "timeout":"7.00:00:00",
                                    "retry":0,
                                    "retryIntervalInSeconds":30,
                                    "secureOutput":false,
                                    "secureInput":false
                                },
                                "userProperties":[  
    
                                ],
                                "typeProperties":{  
                                    "source":{  
                                        "type":"AzureSqlSource",
                                        "sqlReaderQuery":{  
                                            "value":"select * from watermarktable where TableName  =  '@{item().TABLE_NAME}'",
                                            "type":"Expression"
                                        }
                                    },
                                    "dataset":{  
                                        "referenceName":"WatermarkDataset",
                                        "type":"DatasetReference"
                                    }
                                }
                            },
                            {  
                                "name":"LookupNewWaterMarkActivity",
                                "type":"Lookup",
                                "dependsOn":[  
    
                                ],
                                "policy":{  
                                    "timeout":"7.00:00:00",
                                    "retry":0,
                                    "retryIntervalInSeconds":30,
                                    "secureOutput":false,
                                    "secureInput":false
                                },
                                "userProperties":[  
    
                                ],
                                "typeProperties":{  
                                    "source":{  
                                        "type":"SqlServerSource",
                                        "sqlReaderQuery":{  
                                            "value":"select MAX(@{item().WaterMark_Column}) as NewWatermarkvalue from @{item().TABLE_NAME}",
                                            "type":"Expression"
                                        }
                                    },
                                    "dataset":{  
                                        "referenceName":"SourceDataset",
                                        "type":"DatasetReference"
                                    },
                                    "firstRowOnly":true
                                }
                            },
                            {  
                                "name":"IncrementalCopyActivity",
                                "type":"Copy",
                                "dependsOn":[  
                                    {  
                                        "activity":"LookupOldWaterMarkActivity",
                                        "dependencyConditions":[  
                                            "Succeeded"
                                        ]
                                    },
                                    {  
                                        "activity":"LookupNewWaterMarkActivity",
                                        "dependencyConditions":[  
                                            "Succeeded"
                                        ]
                                    }
                                ],
                                "policy":{  
                                    "timeout":"7.00:00:00",
                                    "retry":0,
                                    "retryIntervalInSeconds":30,
                                    "secureOutput":false,
                                    "secureInput":false
                                },
                                "userProperties":[  
    
                                ],
                                "typeProperties":{  
                                    "source":{  
                                        "type":"SqlServerSource",
                                        "sqlReaderQuery":{  
                                            "value":"select * from @{item().TABLE_NAME} where @{item().WaterMark_Column} > '@{activity('LookupOldWaterMarkActivity').output.firstRow.WatermarkValue}' and @{item().WaterMark_Column} <= '@{activity('LookupNewWaterMarkActivity').output.firstRow.NewWatermarkvalue}'",
                                            "type":"Expression"
                                        }
                                    },
                                    "sink":{  
                                        "type":"AzureSqlSink",
                                        "sqlWriterStoredProcedureName":{  
                                            "value":"@{item().StoredProcedureNameForMergeOperation}",
                                            "type":"Expression"
                                        },
                                        "sqlWriterTableType":{  
                                            "value":"@{item().TableType}",
                                            "type":"Expression"
                                        },
                                        "storedProcedureTableTypeParameterName":{  
                                            "value":"@{item().TABLE_NAME}",
                                            "type":"Expression"
                                        },
                                        "disableMetricsCollection":false
                                    },
                                    "enableStaging":false
                                },
                                "inputs":[  
                                    {  
                                        "referenceName":"SourceDataset",
                                        "type":"DatasetReference"
                                    }
                                ],
                                "outputs":[  
                                    {  
                                        "referenceName":"SinkDataset",
                                        "type":"DatasetReference",
                                        "parameters":{  
                                            "SinkTableName":{  
                                                "value":"@{item().TABLE_NAME}",
                                                "type":"Expression"
                                            }
                                        }
                                    }
                                ]
                            },
                            {  
                                "name":"StoredProceduretoWriteWatermarkActivity",
                                "type":"SqlServerStoredProcedure",
                                "dependsOn":[  
                                    {  
                                        "activity":"IncrementalCopyActivity",
                                        "dependencyConditions":[  
                                            "Succeeded"
                                        ]
                                    }
                                ],
                                "policy":{  
                                    "timeout":"7.00:00:00",
                                    "retry":0,
                                    "retryIntervalInSeconds":30,
                                    "secureOutput":false,
                                    "secureInput":false
                                },
                                "userProperties":[  
    
                                ],
                                "typeProperties":{  
                                    "storedProcedureName":"[dbo].[usp_write_watermark]",
                                    "storedProcedureParameters":{  
                                        "LastModifiedtime":{  
                                            "value":{  
                                                "value":"@{activity('LookupNewWaterMarkActivity').output.firstRow.NewWatermarkvalue}",
                                                "type":"Expression"
                                            },
                                            "type":"DateTime"
                                        },
                                        "TableName":{  
                                            "value":{  
                                                "value":"@{activity('LookupOldWaterMarkActivity').output.firstRow.TableName}",
                                                "type":"Expression"
                                            },
                                            "type":"String"
                                        }
                                    }
                                },
                                "linkedServiceName":{  
                                    "referenceName":"AzureSQLDatabaseLinkedService",
                                    "type":"LinkedServiceReference"
                                }
                            }
                        ]
                    }
                }
            ],
            "parameters":{  
                "tableList":{  
                    "type":"array"
                }
            },
            "annotations":[  
    
            ]
        }
    }
    ```
1. Execute o cmdlet **set-AzDataFactoryV2Pipeline** para criar o pipeline IncrementalCopyPipeline.
    
   ```powershell
   Set-AzDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "IncrementalCopyPipeline" -File ".\IncrementalCopyPipeline.json"
   ``` 

   Segue-se o resultado do exemplo: 

   ```json
    PipelineName      : IncrementalCopyPipeline
    ResourceGroupName : <ResourceGroupName>
    DataFactoryName   : <DataFactoryName>
    Activities        : {IterateSQLTables}
    Parameters        : {[tableList, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification]}
   ```
 
## <a name="run-the-pipeline"></a>Executar o pipeline

1. Crie um arquivo de parâmetro chamado **Parameters. JSON** na mesma pasta com o seguinte conteúdo:

    ```json
    {
        "tableList": 
        [
            {
                "TABLE_NAME": "customer_table",
                "WaterMark_Column": "LastModifytime",
                "TableType": "DataTypeforCustomerTable",
                "StoredProcedureNameForMergeOperation": "usp_upsert_customer_table"
            },
            {
                "TABLE_NAME": "project_table",
                "WaterMark_Column": "Creationtime",
                "TableType": "DataTypeforProjectTable",
                "StoredProcedureNameForMergeOperation": "usp_upsert_project_table"
            }
        ]
    }
    ```
1. Execute o pipeline IncrementalCopyPipeline usando o cmdlet **Invoke-AzDataFactoryV2Pipeline** . Substitua os marcadores de posição pelos nomes do seu grupo de recursos e da sua fábrica de dados.

    ```powershell
    $RunId = Invoke-AzDataFactoryV2Pipeline -PipelineName "IncrementalCopyPipeline" -ResourceGroup $resourceGroupName -dataFactoryName $dataFactoryName -ParameterFile ".\Parameters.json"        
    ``` 

## <a name="monitor-the-pipeline"></a>Monitorizar o pipeline

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. Clique em **Todos os serviços**, pesquise com a palavra-chave *Fábricas de dados* e selecione **Fábricas de dados**. 

1. Pesquise pela sua fábrica de dados na lista de fábricas de dados e selecione-a para abrir a página **Fábrica de dados**. 

1. Na página **Data Factory** , selecione **criar & Monitor** para iniciar Azure data Factory em uma guia separada.

1. Na página **vamos começar** , selecione **Monitor** no lado esquerdo. 
![Pipeline execuções ](media/doc-common-process/get-started-page-monitor-button.png)    

1. Pode ver todas as execuções de pipelines e os respetivos estados. Note que no seguinte exemplo, o estado da execução do pipeline é **Com Êxito**. Para verificar os parâmetros transmitidos para o pipeline, selecione a ligação na coluna **Parâmetros**. Se tiver ocorrido um erro, pode ver uma ligação na coluna **Erro**.

    ![Instâncias de Pipeline](media/tutorial-incremental-copy-multiple-tables-powershell/monitor-pipeline-runs-4.png)    
1. Ao selecionar o link na coluna **ações** , você verá todas as execuções de atividade para o pipeline. 

1. Para voltar para a exibição de **execuções de pipeline** , selecione **todas as execuções de pipeline**. 

## <a name="review-the-results"></a>Rever os resultados
No SQL Server Management Studio, execute as seguintes consultas na base de dados SQL de destino para verificar que os dados foram copiados das tabelas de origem para as tabelas de destino. 

**Consulta** 
```sql
select * from customer_table
```

**Saída**
```
===========================================
PersonID    Name    LastModifytime
===========================================
1           John    2017-09-01 00:56:00.000
2           Mike    2017-09-02 05:23:00.000
3           Alice   2017-09-03 02:36:00.000
4           Andy    2017-09-04 03:21:00.000
5           Anny    2017-09-05 08:06:00.000
```

**Consulta**

```sql
select * from project_table
```

**Saída**

```
===================================
Project     Creationtime
===================================
project1    2015-01-01 00:00:00.000
project2    2016-02-02 01:23:00.000
project3    2017-03-04 05:16:00.000
```

**Consulta**

```sql
select * from watermarktable
```

**Saída**

```
======================================
TableName       WatermarkValue
======================================
customer_table  2017-09-05 08:06:00.000
project_table   2017-03-04 05:16:00.000
```

Tenha em atenção que os valores de limite de tamanho de ambas as tabelas foram atualizados. 

## <a name="add-more-data-to-the-source-tables"></a>Adicione mais dados às tabelas de origem

Execute a seguinte consulta na base de dados do SQL Server de origem para atualizar uma linha existente em customer_table. Insira uma linha nova em project_table. 

```sql
UPDATE customer_table
SET [LastModifytime] = '2017-09-08T00:00:00Z', [name]='NewName' where [PersonID] = 3

INSERT INTO project_table
(Project, Creationtime)
VALUES
('NewProject','10/1/2017 0:00:00 AM');
``` 

## <a name="rerun-the-pipeline"></a>Volte a executar o pipeline

1. Agora, execute novamente o pipeline executando o seguinte comando do PowerShell:

    ```powershell
    $RunId = Invoke-AzDataFactoryV2Pipeline -PipelineName "IncrementalCopyPipeline" -ResourceGroup $resourceGroupname -dataFactoryName $dataFactoryName -ParameterFile ".\Parameters.json"
    ```
1. Siga as instruções na secção [Monitorizar o pipeline](#monitor-the-pipeline) para monitorizar as execuções do pipeline. Quando o status do pipeline estiver **em andamento**, você verá outro link de ação em **ações** para cancelar a execução do pipeline. 

1. Selecione **Atualizar** para atualizar a lista até a execução do pipeline ter êxito. 

1. Opcionalmente, selecione a ligação **Ver Execuções de Atividades**, em **Ações**, para ver todas a execuções de atividades associadas a esta execução de pipeline. 

## <a name="review-the-final-results"></a>Rever os resultados finais
No SQL Server Management Studio, execute as seguintes consultas na base de dados de destino para verificar que os dados atualizados/novos foram copiados a partir de tabelas de origem para tabelas de destino. 

**Consulta** 
```sql
select * from customer_table
```

**Saída**
```
===========================================
PersonID    Name    LastModifytime
===========================================
1           John    2017-09-01 00:56:00.000
2           Mike    2017-09-02 05:23:00.000
3           NewName 2017-09-08 00:00:00.000
4           Andy    2017-09-04 03:21:00.000
5           Anny    2017-09-05 08:06:00.000
```

Repare nos valores novos de **Name** e **LastModifytime** para **PersonID** relativamente ao número 3. 

**Consulta**

```sql
select * from project_table
```

**Saída**

```
===================================
Project     Creationtime
===================================
project1    2015-01-01 00:00:00.000
project2    2016-02-02 01:23:00.000
project3    2017-03-04 05:16:00.000
NewProject  2017-10-01 00:00:00.000
```

Repare que a entrada **NewProject** foi adicionada a project_table. 

**Consulta**

```sql
select * from watermarktable
```

**Saída**

```
======================================
TableName       WatermarkValue
======================================
customer_table  2017-09-08 00:00:00.000
project_table   2017-10-01 00:00:00.000
```

Tenha em atenção que os valores de limite de tamanho de ambas as tabelas foram atualizados.
     
## <a name="next-steps"></a>Passos seguintes
Neste tutorial, executou os passos seguintes: 

> [!div class="checklist"]
> * Prepare os arquivos de dados de origem e de destino.
> * Criar uma fábrica de dados.
> * Criou um integration runtime autoalojado (IR).
> * Instalou o integration runtime.
> * Criar serviços ligados. 
> * Crie conjuntos de dados de origem, de sink e de limite de tamanho.
> * Criar, executar e monitorizar um pipeline.
> * Reveja os resultados.
> * Adicionou ou atualizou os dados nas tabelas de origem.
> * Voltou a executar e a monitorizar o pipeline.
> * Rever os resultados finais.

Avance para o tutorial seguinte para saber como transformar dados através de um cluster do Spark no Azure:

> [!div class="nextstepaction"]
>[Carregar dados de forma incremental da Base de Dados SQL do Azure para o armazenamento de Blobs do Azure com a tecnologia de Controlo de Alterações](tutorial-incremental-copy-change-tracking-feature-powershell.md)


