---
title: Copie incrementalmente várias tabelas usando PowerShell
description: Neste tutorial, cria-se uma Azure Data Factory com um pipeline que carrega dados delta de várias tabelas numa base de dados do SQL Server para a Base de Dados Azure SQL.
ms.author: yexu
author: dearandyxu
ms.reviewer: douglasl, maghan
ms.service: data-factory
ms.topic: tutorial
ms.custom: seo-lt-2019; seo-dt-2019
ms.date: 02/18/2021
ms.openlocfilehash: bd29c91efe419ec36b2adc337ecfd1ea7fd71f71
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101739322"
---
# <a name="incrementally-load-data-from-multiple-tables-in-sql-server-to-azure-sql-database-using-powershell"></a>Carregue gradualmente dados de várias tabelas no SQL Server para Azure SQL Database usando PowerShell

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Neste tutorial, cria-se uma Azure Data Factory com um pipeline que carrega dados delta de várias tabelas numa base de dados do SQL Server para a Base de Dados Azure SQL.    

Vai executar os seguintes passos neste tutorial:

> [!div class="checklist"]
> * Prepare os arquivos de dados de origem e de destino.
> * Criar uma fábrica de dados.
> * Criar um integration runtime autoalojado.
> * Instalar o integration runtime. 
> * Criar serviços ligados. 
> * Crie conjuntos de dados de origem, de sink e de marca d'água.
> * Criar, executar e monitorizar um pipeline.
> * Reveja os resultados.
> * Adicionou ou atualizou os dados nas tabelas de origem.
> * Voltou a executar e a monitorizar o pipeline.
> * Reviu os resultados finais.

## <a name="overview"></a>Descrição geral
Eis os passos importantes para criar esta solução: 

1. **Selecionar a coluna de limite de tamanho**.

    Selecione uma coluna para cada tabela na loja de dados de origem, que pode identificar os registos novos ou atualizados para cada execução. Normalmente, os dados nesta coluna selecionada (por exemplo, last_modify_time ou ID) continuam a aumentar quando as linhas são criadas ou atualizadas. O valor máximo nesta coluna é utilizado como limite de tamanho.

2. **Preparar um arquivo de dados para armazenar o valor de limite de tamanho**.

    Neste tutorial, vai armazenar o valor de marca d'água numa base de dados SQL.

3. **Criar um pipeline com as seguintes atividades:**
    
    a. Criar uma atividade ForEach que itera através de uma lista de nomes de tabelas de origem que é transmitida como um parâmetro para o pipeline. Para cada tabela de origem, este invoca as seguintes atividades para efetuar o carregamento de diferenças para essa tabela.

    b. Criar duas atividades de pesquisa. Utilize a primeira atividade Lookup para obter o último valor de limite de tamanho. Utilize a segunda para obter o valor de limite de tamanho novo. Estes valores de limite de tamanho são transmitidos para a atividade Copy.

    c. Criar uma atividade Cópia que copia linhas do arquivo de dados de origem com o valor da coluna de limite de tamanho superior ao valor de limite de tamanho antigo e inferior ao valor novo. Em seguida, copia os dados delta do arquivo de dados de origem para o armazenamento de Blobs do Azure como um ficheiro novo.

    d. Crie uma atividade StoredProcedure, que atualiza o valor de marca d'água do pipeline que vai ser executado da próxima vez. 

    Eis o diagrama de nível elevado da solução: 

    ![Carregar dados de forma incremental](media/tutorial-incremental-copy-multiple-tables-powershell/high-level-solution-diagram.png)


Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

* **Sql Server**. Utiliza uma base de dados SQL Server como base de dados de origem neste tutorial. 
* **Base de Dados Azure SQL**. Utiliza uma base de dados na Base de Dados Azure SQL como loja de dados da pia. Se não tiver uma base de dados SQL, consulte [criar uma base de dados na Base de Dados Azure SQL](../azure-sql/database/single-database-create-quickstart.md) para obter etapas para criar uma. 

### <a name="create-source-tables-in-your-sql-server-database"></a>Criar tabelas de origem na base de dados do SQL Server

1. Abra [o SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms) ou [Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio)e ligue-se à sua base de dados SQL Server.

2. No **Server Explorer (SSMS)** ou no **painel Connections (Azure Data Studio)**, clique à direita na base de dados e escolha **Nova Consulta**.

3. Execute o seguinte comando SQL na base de dados para criar tabelas com o nome `customer_table` e `project_table`:

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

### <a name="create-destination-tables-in-your-azure-sql-database"></a>Crie tabelas de destino na sua Base de Dados Azure SQL

1. Abra [o SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms) ou [Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio)e ligue-se à sua base de dados SQL Server.

2. No **Server Explorer (SSMS)** ou no **painel Connections (Azure Data Studio)**, clique à direita na base de dados e escolha **Nova Consulta**.

3. Execute o seguinte comando SQL na base de dados para criar tabelas com o nome `customer_table` e `project_table`:  

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

### <a name="create-another-table-in-azure-sql-database-to-store-the-high-watermark-value"></a>Criar outra tabela na Base de Dados Azure SQL para armazenar o elevado valor da marca de água

1. Executar o seguinte comando SQL contra a sua base de dados para criar uma tabela com o nome `watermarktable` para armazenar o valor da marca de água: 
    
    ```sql
    create table watermarktable
    (
    
        TableName varchar(255),
        WatermarkValue datetime,
    );
    ```
2. Inserir valores de marca d'água iniciais para ambas as tabelas de origem na tabela de marca d'água.

    ```sql

    INSERT INTO watermarktable
    VALUES 
    ('customer_table','1/1/2010 12:00:00 AM'),
    ('project_table','1/1/2010 12:00:00 AM');
    
    ```

### <a name="create-a-stored-procedure-in-the-azure-sql-database"></a>Criar um procedimento armazenado na Base de Dados Azure SQL 

Execute o seguinte comando para criar um procedimento armazenado na sua base de dados. Este procedimento armazenado atualiza o valor de limite de tamanho após cada execução de pipeline. 

```sql
CREATE PROCEDURE usp_write_watermark @LastModifiedtime datetime, @TableName varchar(50)
AS

BEGIN

UPDATE watermarktable
SET [WatermarkValue] = @LastModifiedtime 
WHERE [TableName] = @TableName

END

```

### <a name="create-data-types-and-additional-stored-procedures-in-azure-sql-database"></a>Criar tipos de dados e procedimentos adicionais armazenados na Base de Dados Azure SQL

Execute a seguinte consulta para criar dois procedimentos armazenados e dois tipos de dados na sua base de dados. São utilizados para intercalar os dados das tabelas de origem nas tabelas de destino. 

Para facilitar a viagem, utilizamos diretamente estes Procedimentos Armazenados passando os dados delta através de uma variável de tabela e, em seguida, fundimo-los em loja de destino. Tenha cuidado, não espera que um número "grande" de linhas delta (mais de 100) seja armazenado na variável da tabela.  

Se precisa de fundir um grande número de linhas delta na loja de destino, sugerimos que utilize a atividade de cópia para copiar todos os dados delta numa tabela temporária de "staging" na loja de destino, e depois construiu o seu próprio procedimento armazenado sem usar a variável de mesa para os fundir da mesa de "encenação" para a tabela "final". 


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

1. Defina uma variável para o nome do grupo de recursos que vai utilizar nos comandos do PowerShell mais tarde. Copie o texto do comando seguinte para o PowerShell, especifique um nome para o [Grupo de recursos do Azure](../azure-resource-manager/management/overview.md) com aspas duplas e execute o comando. Um exemplo é `"adfrg"`.

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
    >  Atualize o nome da fábrica de dados, para que seja globalmente exclusivo. Um exemplo é ADFIncMultiCopyTutorialFactorySP1127. 

    ```powershell
    $dataFactoryName = "ADFIncMultiCopyTutorialFactory";
    ```
5. Para criar a fábrica de dados, executar o seguinte **Set-AzDataFactoryV2** cmdlet: 
    
    ```powershell
    Set-AzDataFactoryV2 -ResourceGroupName $resourceGroupName -Location $location -Name $dataFactoryName 
    ```

Tenha em atenção os seguintes pontos:

* O nome da fábrica de dados tem de ser globalmente exclusivo. Se receber o erro seguinte, altere o nome e tente novamente:

    ```powershell
    Set-AzDataFactoryV2 : HTTP Status Code: Conflict
    Error Code: DataFactoryNameInUse
    Error Message: The specified resource name 'ADFIncMultiCopyTutorialFactory' is already in use. Resource names must be globally unique.
    ```

* Para criar instâncias do Data Factory, a conta de utilizador que utiliza para iniciar sessão no Azure tem de ser membro das funções contribuidor ou proprietário ou administrador da subscrição do Azure.

* Para obter uma lista de regiões do Azure em que o Data Factory está atualmente disponível, selecione as regiões que lhe interessam na página seguinte e, em seguida, expanda **Analytics** para localizar **Data Factory**: [Produtos disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/). As lojas de dados (Azure Storage, SQL Database, SQL Managed Instance, e assim por diante) e computas (Azure HDInsight, etc.) utilizadas pela fábrica de dados podem estar noutras regiões.

[!INCLUDE [data-factory-create-install-integration-runtime](../../includes/data-factory-create-install-integration-runtime.md)]

## <a name="create-linked-services"></a>Criar serviços ligados

Os serviços ligados são criados numa fábrica de dados para ligar os seus arquivos de dados e serviços de computação a essa fábrica de dados. Nesta secção, crie serviços ligados à base de dados do SQL Server e à sua base de dados na Base de Dados Azure SQL. 

### <a name="create-the-sql-server-linked-service"></a>Criar o serviço ligado do SQL Server

Neste passo, ligue a sua base de dados SQL Server à fábrica de dados.

1. Crie um ficheiro JSON nomeado **SqlServerLinkedService.jsna** pasta C:\ADFTutorials\IncCopyMultiTableTutorial (crie as pastas locais se já não existirem) com o seguinte conteúdo. Selecione a secção certa com base na autenticação que utiliza para se ligar ao SQL Server.  

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

2. Em PowerShell, passe o cmdlet seguinte para mudar para a pasta C:\ADFTutorials\IncCopyMultiTableTutorial.

    ```powershell
    Set-Location 'C:\ADFTutorials\IncCopyMultiTableTutorial'
    ```

3. Executar o **set-AzDataFactoryV2LinkedService** para criar o serviço ligado AzureStorageLinkedService. No exemplo seguinte, passa-se valores para os parâmetros *DataGroupName* e *DataFactoryName:* 

    ```powershell
    Set-AzDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "SqlServerLinkedService" -File ".\SqlServerLinkedService.json"
    ```

    Segue-se o resultado do exemplo:

    ```console
    LinkedServiceName : SqlServerLinkedService
    ResourceGroupName : <ResourceGroupName>
    DataFactoryName   : <DataFactoryName>
    Properties        : Microsoft.Azure.Management.DataFactory.Models.SqlServerLinkedService
    ```

### <a name="create-the-sql-database-linked-service"></a>Criar o serviço de base de dados SQL ligado

1. Crie um ficheiro JSON nomeado **AzureSQLDatabaseLinkedService.jsem** C:\ADFTutorials\IncCopyMultiTableTutorial com o seguinte conteúdo. (Criar a pasta ADF se já não existir.) Substitua &lt; o nome de &gt; servidor, nome de base de &lt; &gt; &lt; dados, nome de utilizador &gt; e &lt; palavra-passe &gt; pelo nome da base de dados do SEU SQL Server, nome da sua base de dados, nome de utilizador e palavra-passe antes de guardar o ficheiro. 

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
2. Em PowerShell, executar o **cmdlet Set-AzDataFactoryV2LinkedService** para criar o serviço ligado AzureSQLDatabaseLinkedService. 

    ```powershell
    Set-AzDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureSQLDatabaseLinkedService" -File ".\AzureSQLDatabaseLinkedService.json"
    ```

    Segue-se o resultado do exemplo:

    ```console
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

2. Executar o **cmdlet Set-AzDataFactoryV2Dataset** para criar o conjunto de dados SourceDataset.
    
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

1. Crie um ficheiro JSON nomeado **SinkDataset.jsna** mesma pasta com o seguinte conteúdo. O elemento tableName é definido pelo pipeline dinamicamente durante a execução. A atividade ForEach no pipeline itera através de uma lista de nomes de tabelas e transmite o nome da tabela para este conjunto de dados em cada iteração. 

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

2. Executar o **cmdlet Set-AzDataFactoryV2Dataset** para criar o conjunto de dados SinkDataset.
    
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

### <a name="create-a-dataset-for-a-watermark"></a>Criar um conjunto de dados para uma marca d'água

Neste passo, vai criar um conjunto de dados para armazenar um valor de limite superior de tamanho. 

1. Criar um ficheiro JSON nomeado **WatermarkDataset.jsna** mesma pasta com o seguinte conteúdo: 

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
2. Executar o **cmdlet Set-AzDataFactoryV2Dataset** para criar o conjunto de dados WatermarkDataset.
    
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

O pipeline aceita uma lista de nomes de tabela como parâmetro. A **atividade ForEach** iteração através da lista de nomes de tabelas e executa as seguintes operações: 

1. Utilize a **atividade Desacurar** para recuperar o valor da marca de água antiga (o valor inicial ou o que foi utilizado na última iteração).

2. Utilize a **atividade Desacurar** para recuperar o novo valor da marca de água (o valor máximo da coluna de marca de água no quadro de origem).

3. Utilize a **atividade Copy** para copiar dados entre estes dois valores de marca de água da base de dados de origem para a base de dados de destino.

4. Utilize a **atividade StoredProcedure** para atualizar o valor da marca de água a utilizar no primeiro passo da iteração seguinte. 

### <a name="create-the-pipeline"></a>Criar o pipeline

1. Criar um ficheiro JSON nomeado **IncrementalCopyPipeline.jsna** mesma pasta com o seguinte conteúdo: 

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
2. Executar o **cmdlet Set-AzDataFactoryV2Pipeline** para criar o pipeline IncrementalCopyPipeline.
    
   ```powershell
   Set-AzDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "IncrementalCopyPipeline" -File ".\IncrementalCopyPipeline.json"
   ``` 

   Segue-se o resultado do exemplo: 

   ```console
    PipelineName      : IncrementalCopyPipeline
    ResourceGroupName : <ResourceGroupName>
    DataFactoryName   : <DataFactoryName>
    Activities        : {IterateSQLTables}
    Parameters        : {[tableList, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification]}
   ```
 
## <a name="run-the-pipeline"></a>Executar o pipeline

1. Criar um ficheiro de parâmetros denominado **Parameters.jsna** mesma pasta com o seguinte conteúdo:

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
2. Executar o pipeline IncrementalCopyPipeline utilizando o **cmdlet Invoke-AzDataFactoryV2Pipeline.** Substitua os marcadores de posição pelos nomes do seu grupo de recursos e da sua fábrica de dados.

    ```powershell
    $RunId = Invoke-AzDataFactoryV2Pipeline -PipelineName "IncrementalCopyPipeline" -ResourceGroup $resourceGroupName -dataFactoryName $dataFactoryName -ParameterFile ".\Parameters.json"        
    ``` 

## <a name="monitor-the-pipeline"></a>Monitorizar o pipeline

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

2. Clique em **Todos os serviços**, pesquise com a palavra-chave *Fábricas de dados* e selecione **Fábricas de dados**. 

3. Pesquise pela sua fábrica de dados na lista de fábricas de dados e selecione-a para abrir a página **Fábrica de dados**. 

4. Na página da **fábrica de dados,** selecione **Author & Monitor** para lançar a Azure Data Factory num separador.

5. Na página **Let's get start,** selecione **Monitor** no lado esquerdo. 
![A screenshot mostra a página Let's get start for Azure Data Factory.](media/doc-common-process/get-started-page-monitor-button.png)    

6. Pode ver todas as execuções de pipelines e os respetivos estados. Note que no seguinte exemplo, o estado da execução do pipeline é **Com Êxito**. Para verificar os parâmetros transmitidos para o pipeline, selecione a ligação na coluna **Parâmetros**. Se tiver ocorrido um erro, pode ver uma ligação na coluna **Erro**.

    ![A screenshot mostra que o gasoduto funciona para uma fábrica de dados, incluindo o seu oleoduto.](media/tutorial-incremental-copy-multiple-tables-powershell/monitor-pipeline-runs-4.png)    
7. Quando selecionar o link na coluna **Ações,** vê toda a atividade a correr para o oleoduto. 

8. Para voltar à vista **Pipeline Runs,** selecione **All Pipeline Runs**. 

## <a name="review-the-results"></a>Rever os resultados

No SQL Server Management Studio, execute as seguintes consultas na base de dados SQL de destino para verificar que os dados foram copiados das tabelas de origem para as tabelas de destino. 

**Query** 
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

**Query**

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

**Query**

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
2. Siga as instruções na secção [Monitorizar o pipeline](#monitor-the-pipeline) para monitorizar as execuções do pipeline. Quando o estado do gasoduto estiver **em andamento,** vê-se outro link de ação no âmbito **das Ações** para cancelar a execução do gasoduto. 

3. Selecione **Atualizar** para atualizar a lista até a execução do pipeline ter êxito. 

4. Opcionalmente, selecione a ligação **Ver Execuções de Atividades**, em **Ações**, para ver todas a execuções de atividades associadas a esta execução de pipeline. 

## <a name="review-the-final-results"></a>Rever os resultados finais

No SQL Server Management Studio, execute as seguintes consultas na base de dados de destino para verificar que os dados atualizados/novos foram copiados a partir de tabelas de origem para tabelas de destino. 

**Query** 
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

**Query**

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

**Query**

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
> * Instalar o integration runtime.
> * Criar serviços ligados. 
> * Crie conjuntos de dados de origem, de sink e de marca d'água.
> * Criar, executar e monitorizar um pipeline.
> * Reveja os resultados.
> * Adicionou ou atualizou os dados nas tabelas de origem.
> * Voltou a executar e a monitorizar o pipeline.
> * Reviu os resultados finais.

Avance para o tutorial seguinte para saber como transformar dados através de um cluster do Spark no Azure:

> [!div class="nextstepaction"]
>[Carregar dados de forma incremental da Base de Dados SQL do Azure para o armazenamento de Blobs do Azure com a tecnologia de Controlo de Alterações](tutorial-incremental-copy-change-tracking-feature-powershell.md)
