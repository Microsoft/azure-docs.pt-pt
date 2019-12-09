---
title: SQL Server atividade de procedimento armazenado
description: Saiba como você pode usar a SQL Server atividade de procedimento armazenado para invocar um procedimento armazenado em um banco de dados SQL do Azure ou no Azure SQL Data Warehouse de um pipeline Data Factory.
services: data-factory
documentationcenter: ''
ms.assetid: 1c46ed69-4049-44ec-9b46-e90e964a4a8e
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
author: nabhishek
ms.author: abnarain
manager: anandsub
robots: noindex
ms.openlocfilehash: 45aa49de51f42b26c653b15e79c865e3f5647c39
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74931633"
---
# <a name="sql-server-stored-procedure-activity"></a>SQL Server atividade de procedimento armazenado
> [!div class="op_single_selector" title1="Atividades de transformação"]
> * [Atividade do hive](data-factory-hive-activity.md)
> * [Atividade Pig](data-factory-pig-activity.md)
> * [Atividade MapReduce](data-factory-map-reduce.md)
> * [Atividade de streaming do Hadoop](data-factory-hadoop-streaming-activity.md)
> * [Atividade do Spark](data-factory-spark.md)
> * [Atividade de Execução em Lote do Machine Learning](data-factory-azure-ml-batch-execution-activity.md)
> * [Atividade de Recursos de Atualização de Machine Learning](data-factory-azure-ml-update-resource-activity.md)
> * [Atividade de Procedimento Armazenado](data-factory-stored-proc-activity.md)
> * [Atividade de U-SQL do Data Lake Analytics](data-factory-usql-activity.md)
> * [Atividade personalizada do .NET](data-factory-use-custom-activities.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Azure Data Factory. Se você estiver usando a versão atual do serviço de Data Factory, consulte [transformar dados usando a atividade de procedimento armazenado no data Factory](../transform-data-using-stored-procedure.md).

## <a name="overview"></a>Visão geral
Você usa atividades de transformação de dados em um [pipeline](data-factory-create-pipelines.md) data Factory para transformar e processar dados brutos em previsões e ideias. A atividade de procedimento armazenado é uma das atividades de transformação às quais Data Factory dá suporte. Este artigo se baseia no artigo [atividades de transformação de dados](data-factory-data-transformation-activities.md) , que apresenta uma visão geral da transformação de dados e das atividades de transformação com suporte no data Factory.

Você pode usar a atividade de procedimento armazenado para invocar um procedimento armazenado em um dos seguintes repositórios de dados em sua empresa ou em uma VM (máquina virtual) do Azure:

- Base de Dados SQL do Azure
- Armazém de Dados SQL do Azure
- SQL Server banco de dados. Se você estiver usando SQL Server, instale Gerenciamento de Dados gateway no mesmo computador que hospeda o banco de dados ou em um computador separado que tenha acesso ao banco de dados. Gerenciamento de Dados gateway é um componente que conecta fontes de dados locais/na VM do Azure com serviços de nuvem de maneira segura e gerenciada. Consulte o artigo [Gerenciamento de dados gateway](data-factory-data-management-gateway.md) para obter detalhes.

> [!IMPORTANT]
> Durante a cópia de dados no Azure SQL Database ou SQL Server, você pode configurar o **sqlsink** na atividade de cópia para invocar um procedimento armazenado usando a propriedade **sqlWriterStoredProcedureName** . Para obter mais informações, consulte [invocar procedimento armazenado da atividade de cópia](data-factory-invoke-stored-procedure-from-copy-activity.md). Para obter detalhes sobre a propriedade, consulte os seguintes artigos de conector: [banco de dados SQL do Azure](data-factory-azure-sql-connector.md#copy-activity-properties), [SQL Server](data-factory-sqlserver-connector.md#copy-activity-properties). Não há suporte para invocar um procedimento armazenado ao copiar dados em uma SQL Data Warehouse do Azure usando uma atividade de cópia. Mas, você pode usar a atividade de procedimento armazenado para invocar um procedimento armazenado em um SQL Data Warehouse.
>
> Ao copiar dados do Azure SQL Database ou SQL Server ou do SQL Data Warehouse do Azure, você pode configurar **sqlsource** na atividade de cópia para invocar um procedimento armazenado para ler dados do banco de dados de origem usando a propriedade **sqlReaderStoredProcedureName** . Para obter mais informações, consulte os seguintes artigos de conector: [banco de dados SQL do Azure](data-factory-azure-sql-connector.md#copy-activity-properties), [SQL Server](data-factory-sqlserver-connector.md#copy-activity-properties), [Azure SQL data warehouse](data-factory-azure-sql-data-warehouse-connector.md#copy-activity-properties)

A instrução a seguir usa a atividade de procedimento armazenado em um pipeline para invocar um procedimento armazenado em um banco de dados SQL do Azure.

## <a name="walkthrough"></a>Instruções
### <a name="sample-table-and-stored-procedure"></a>Tabela de exemplo e procedimento armazenado
1. Crie a **tabela** a seguir no banco de dados SQL do Azure usando SQL Server Management Studio ou qualquer outra ferramenta com a qual você esteja familiarizado. A coluna DateTimeStamp é a data e a hora em que a ID correspondente é gerada.

    ```SQL
    CREATE TABLE dbo.sampletable
    (
        Id uniqueidentifier,
        datetimestamp nvarchar(127)
    )
    GO

    CREATE CLUSTERED INDEX ClusteredID ON dbo.sampletable(Id);
    GO
    ```
    ID é o exclusivo identificado e a coluna DateTimeStamp é a data e a hora em que a ID correspondente é gerada.
    
    ![Dados de exemplo](./media/data-factory-stored-proc-activity/sample-data.png)

    Neste exemplo, o procedimento armazenado está em um banco de dados SQL do Azure. Se o procedimento armazenado estiver em um SQL Data Warehouse do Azure e SQL Server banco de dados, a abordagem será semelhante. Para um banco de dados SQL Server, você deve instalar um [Gateway de gerenciamento de dados](data-factory-data-management-gateway.md).
2. Crie o **procedimento armazenado** a seguir que insere dados no **exemplo**.

    ```SQL
    CREATE PROCEDURE usp_sample @DateTime nvarchar(127)
    AS

    BEGIN
        INSERT INTO [sampletable]
        VALUES (newid(), @DateTime)
    END
    ```

   > [!IMPORTANT]
   > O **nome** e a **capitalização** do parâmetro (DateTime neste exemplo) devem corresponder ao parâmetro especificado no JSON de atividade/pipeline. Na definição do procedimento armazenado, verifique se **\@** é usada como um prefixo para o parâmetro.

### <a name="create-a-data-factory"></a>Criar uma fábrica de dados
1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
2. Clique em **novo** no menu à esquerda, clique em **inteligência + análise**e clique em **Data Factory**.

    ![Nova fábrica de dados](media/data-factory-stored-proc-activity/new-data-factory.png)
3. Na folha **novo data Factory** , digite **SProcDF** para o nome. Os nomes de Azure Data Factory são **globalmente exclusivos**. Você precisa prefixar o nome do data factory com seu nome, para habilitar a criação bem-sucedida da fábrica.

   ![Nova fábrica de dados](media/data-factory-stored-proc-activity/new-data-factory-blade.png)
4. Selecione a sua **subscrição** do Azure.
5. Para **Grupo de Recursos**, siga um destes passos:
   1. Clique em **criar novo** e insira um nome para o grupo de recursos.
   2. Clique em **usar existente** e selecione um grupo de recursos existente.
6. Selecione a **localização** da fábrica de dados.
7. Selecione **fixar no painel** para que você possa ver o data Factory no painel da próxima vez que fizer logon.
8. Clique em **Criar** no painel **Nova fábrica de dados**.
9. Você verá o data factory que está sendo criado no **painel** do portal do Azure. Depois de ter criado a fábrica de dados com êxito, vê a página da fábrica de dados e o respetivo conteúdo.

   ![Data Factory home page](media/data-factory-stored-proc-activity/data-factory-home-page.png)

### <a name="create-an-azure-sql-linked-service"></a>Criar um serviço vinculado do SQL do Azure
Depois de criar o data factory, você cria um serviço vinculado do SQL do Azure que vincula o banco de dados SQL do Azure, que contém a tabela de exemplo e usp_sample procedimento armazenado, ao seu data factory.

1. Clique em **criar e implantar** na folha **Data Factory** para **SProcDF** para iniciar o editor de data Factory.
2. Clique em **novo armazenamento de dados** na barra de comandos e escolha banco de dado **SQL do Azure**. Você deve ver o script JSON para criar um serviço vinculado do SQL do Azure no editor.

   ![Novo armazenamento de dados](media/data-factory-stored-proc-activity/new-data-store.png)
3. No script JSON, faça as seguintes alterações:

   1. Substitua `<servername>` pelo nome do seu servidor de banco de dados SQL do Azure.
   2. Substitua `<databasename>` pelo banco de dados no qual você criou a tabela e o procedimento armazenado.
   3. Substitua `<username@servername>` pela conta de usuário que tem acesso ao banco de dados.
   4. Substitua `<password>` pela senha da conta de usuário.

      ![Novo armazenamento de dados](media/data-factory-stored-proc-activity/azure-sql-linked-service.png)
4. Para implantar o serviço vinculado, clique em **implantar** na barra de comandos. Confirme que você vê o AzureSqlLinkedService no modo de exibição de árvore à esquerda.

    ![modo de exibição de árvore com serviço vinculado](media/data-factory-stored-proc-activity/tree-view.png)

### <a name="create-an-output-dataset"></a>Criar um conjunto de dados de saída
Você deve especificar um conjunto de dados de saída para uma atividade de procedimento armazenado, mesmo que o procedimento armazenado não produza nenhum dado. Isso ocorre porque é o conjunto de resultados de saída que orienta o agendamento da atividade (com que frequência a atividade é executada-por hora, diariamente, etc.). O conjunto de dados de saída deve usar um **serviço vinculado** que se refere a um banco de dados SQL do Azure ou a um SQL data warehouse do Azure ou a um banco de dados SQL Server no qual você deseja que o procedimento armazenado seja executado. O conjunto de resultados de saída pode servir como uma maneira de passar o resultado do procedimento armazenado para processamento subsequente por outra atividade ([encadeando atividades](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline) no pipeline. No entanto, Data Factory não grava automaticamente a saída de um procedimento armazenado para esse conjunto de um. É o procedimento armazenado que grava em uma tabela SQL para a qual o conjunto de resultados de saída aponta. Em alguns casos, o conjunto de resultados de saída pode ser um conjunto de um **DataSet** (um conjunto de um que aponta para uma tabela que, na verdade, não mantém a saída do procedimento armazenado). Esse conjunto de DataSet fictício é usado apenas para especificar o agendamento para a execução da atividade de procedimento armazenado.

1. Clique em...  **Mais** na barra de ferramentas, clique em **novo conjunto**de informações e clique em **SQL do Azure**. **Novo DataSet** na barra de comandos e selecione **SQL do Azure**.

    ![modo de exibição de árvore com serviço vinculado](media/data-factory-stored-proc-activity/new-dataset.png)
2. Copie/cole o script JSON a seguir no editor de JSON.

    ```JSON
    {
        "name": "sprocsampleout",
        "properties": {
            "type": "AzureSqlTable",
            "linkedServiceName": "AzureSqlLinkedService",
            "typeProperties": {
                "tableName": "sampletable"
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            }
        }
    }
    ```
3. Para implantar o conjunto de um, clique em **implantar** na barra de comandos. Confirme que você vê o conjunto de os no modo de exibição de árvore.

    ![modo de exibição de árvore com serviços vinculados](media/data-factory-stored-proc-activity/tree-view-2.png)

### <a name="create-a-pipeline-with-sqlserverstoredprocedure-activity"></a>Criar um pipeline com a atividade SqlServerStoredProcedure
Agora, vamos criar um pipeline com uma atividade de procedimento armazenado.

Observe as seguintes propriedades:

- A propriedade **Type** é definida como **SqlServerStoredProcedure**.
- O **storedprocedurename** em Propriedades de tipo é definido como **usp_sample** (nome do procedimento armazenado).
- A seção **storedprocedureparameters** contém um parâmetro chamado **DateTime**. O nome e a capitalização do parâmetro em JSON devem corresponder ao nome e à capitalização do parâmetro na definição do procedimento armazenado. Se você precisar passar NULL para um parâmetro, use a sintaxe: `"param1": null` (todas as letras minúsculas).

1. Clique em...  **Mais** na barra de comandos e clique em **novo pipeline**.
2. Copie/cole o seguinte trecho JSON:

    ```JSON
    {
        "name": "SprocActivitySamplePipeline",
        "properties": {
            "activities": [
                {
                    "type": "SqlServerStoredProcedure",
                    "typeProperties": {
                        "storedProcedureName": "usp_sample",
                        "storedProcedureParameters": {
                            "DateTime": "$$Text.Format('{0:yyyy-MM-dd HH:mm:ss}', SliceStart)"
                        }
                    },
                    "outputs": [
                        {
                            "name": "sprocsampleout"
                        }
                    ],
                    "scheduler": {
                        "frequency": "Hour",
                        "interval": 1
                    },
                    "name": "SprocActivitySample"
                }
            ],
            "start": "2017-04-02T00:00:00Z",
            "end": "2017-04-02T05:00:00Z",
            "isPaused": false
        }
    }
    ```
3. Para implantar o pipeline, clique em **implantar** na barra de ferramentas.

### <a name="monitor-the-pipeline"></a>Monitorizar o pipeline
1. Clique em **X** para fechar os painéis do Editor do Data Factory e para regressar ao painel Data Factory e, em seguida, clique em **Diagrama**.

    ![bloco de diagrama](media/data-factory-stored-proc-activity/data-factory-diagram-tile.png)
2. Na **Vista de Diagrama**, verá uma descrição geral dos pipelines e dos conjuntos de dados utilizados neste tutorial.

    ![bloco de diagrama](media/data-factory-stored-proc-activity/data-factory-diagram-view.png)
3. No modo de exibição de diagrama, clique duas vezes no conjunto de `sprocsampleout`DataSet. Você verá as fatias no estado pronto. Deve haver cinco fatias porque uma fatia é produzida para cada hora entre a hora de início e a hora de término do JSON.

    ![bloco de diagrama](media/data-factory-stored-proc-activity/data-factory-slices.png)
4. Quando uma fatia estiver no estado **pronto** , execute uma consulta de `select * from sampletable` no banco de dados SQL do Azure para verificar se eles foram inseridos na tabela pelo procedimento armazenado.

   ![Dados de saída](./media/data-factory-stored-proc-activity/output.png)

   Consulte [monitorar o pipeline](data-factory-monitor-manage-pipelines.md) para obter informações detalhadas sobre como monitorar pipelines de Azure data Factory.

## <a name="specify-an-input-dataset"></a>Especificar um conjunto de dados de entrada
Na instrução, a atividade de procedimento armazenado não tem nenhum conjunto de dados de entrada. Se você especificar um conjunto de dados de entrada, a atividade de procedimento armazenado não será executada até que a fatia do conjunto de dados de entrada esteja disponível (no estado pronto). O conjunto de uma pode ser um conjunto de um DataSet externo (que não é produzido por outra atividade no mesmo Pipeline) ou um conjunto de um DataSet interno que é produzido por uma atividade upstream (a atividade que é executada antes dessa atividade). Você pode especificar vários conjuntos de dados de entrada para a atividade de procedimento armazenado. Se você fizer isso, a atividade de procedimento armazenado só será executada quando todas as fatias do conjunto de dados de entrada estiverem disponíveis (no estado pronto). O conjunto de dados de entrada não pode ser consumido no procedimento armazenado como um parâmetro. Ele é usado apenas para verificar a dependência antes de iniciar a atividade de procedimento armazenado.

## <a name="chaining-with-other-activities"></a>Encadeamento com outras atividades
Se você quiser encadear uma atividade upstream com essa atividade, especifique a saída da atividade upstream como uma entrada dessa atividade. Quando você fizer isso, a atividade de procedimento armazenado não será executada até que a atividade upstream seja concluída e o conjunto de resultados de saída da atividade upstream esteja disponível (em status pronto). Você pode especificar conjuntos de dados de saída de várias atividades upstream como conjuntos de dados de entrada da atividade de procedimento armazenado. Quando você fizer isso, a atividade de procedimento armazenado só será executada quando todas as fatias do conjunto de dados de entrada estiverem disponíveis.

No exemplo a seguir, a saída da atividade de cópia é: OutputDataset, que é uma entrada da atividade de procedimento armazenado. Portanto, a atividade de procedimento armazenado não é executada até que a atividade de cópia seja concluída e a fatia OutputDataset esteja disponível (no estado pronto). Se você especificar vários conjuntos de dados de entrada, a atividade de procedimento armazenado não será executada até que todas as fatias do conjunto de dados de entrada estejam disponíveis (no estado pronto). Os conjuntos de dados de entrada não podem ser usados diretamente como parâmetros para a atividade de procedimento armazenado.

Para obter mais informações sobre as atividades de encadeamento, consulte [várias atividades em um pipeline](data-factory-create-pipelines.md#multiple-activities-in-a-pipeline)

```json
{
    "name": "ADFTutorialPipeline",
    "properties": {
        "description": "Copy data from a blob to blob",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [ { "name": "InputDataset" } ],
                "outputs": [ { "name": "OutputDataset" } ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst"
                },
                "name": "CopyFromBlobToSQL"
            },
            {
                "type": "SqlServerStoredProcedure",
                "typeProperties": {
                    "storedProcedureName": "SPSproc"
                },
                "inputs": [ { "name": "OutputDataset" } ],
                "outputs": [ { "name": "SQLOutputDataset" } ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1,
                    "retry": 3
                },
                "name": "RunStoredProcedure"
            }
        ],
        "start": "2017-04-12T00:00:00Z",
        "end": "2017-04-13T00:00:00Z",
        "isPaused": false,
    }
}
```

Da mesma forma, para vincular a atividade de procedimento de armazenamento com **atividades de downstream** (as atividades executadas após a conclusão da atividade de procedimento armazenado), especifique o conjunto de dados de saída da atividade de procedimento armazenado como uma entrada da atividade downstream no pipeline.

> [!IMPORTANT]
> Durante a cópia de dados no Azure SQL Database ou SQL Server, você pode configurar o **sqlsink** na atividade de cópia para invocar um procedimento armazenado usando a propriedade **sqlWriterStoredProcedureName** . Para obter mais informações, consulte [invocar procedimento armazenado da atividade de cópia](data-factory-invoke-stored-procedure-from-copy-activity.md). Para obter detalhes sobre a propriedade, consulte os seguintes artigos de conector: [banco de dados SQL do Azure](data-factory-azure-sql-connector.md#copy-activity-properties), [SQL Server](data-factory-sqlserver-connector.md#copy-activity-properties).
> 
> Ao copiar dados do Azure SQL Database ou SQL Server ou do SQL Data Warehouse do Azure, você pode configurar **sqlsource** na atividade de cópia para invocar um procedimento armazenado para ler dados do banco de dados de origem usando a propriedade **sqlReaderStoredProcedureName** . Para obter mais informações, consulte os seguintes artigos de conector: [banco de dados SQL do Azure](data-factory-azure-sql-connector.md#copy-activity-properties), [SQL Server](data-factory-sqlserver-connector.md#copy-activity-properties), [Azure SQL data warehouse](data-factory-azure-sql-data-warehouse-connector.md#copy-activity-properties)

## <a name="json-format"></a>Formato JSON
Este é o formato JSON para definir uma atividade de procedimento armazenado:

```JSON
{
    "name": "SQLSPROCActivity",
    "description": "description",
    "type": "SqlServerStoredProcedure",
    "inputs": [ { "name": "inputtable" } ],
    "outputs": [ { "name": "outputtable" } ],
    "typeProperties":
    {
        "storedProcedureName": "<name of the stored procedure>",
        "storedProcedureParameters":
        {
            "param1": "param1Value"
            …
        }
    }
}
```

A tabela a seguir descreve essas propriedades JSON:

| Propriedade | Descrição | Obrigatório |
| --- | --- | --- |
| nome | Nome da atividade |Sim |
| descrição |Texto que descreve para que a atividade é usada |Não |
| tipo | Deve ser definido como: **SqlServerStoredProcedure** | Sim |
| inputs | Opcional. Se você especificar um conjunto de dados de entrada, ele deverá estar disponível (no status ' pronto ') para que a atividade de procedimento armazenado seja executada. O conjunto de dados de entrada não pode ser consumido no procedimento armazenado como um parâmetro. Ele é usado apenas para verificar a dependência antes de iniciar a atividade de procedimento armazenado. |Não |
| outputs | Você deve especificar um conjunto de uma saída para uma atividade de procedimento armazenado. O conjunto de resultados de saída especifica o **agendamento** para a atividade de procedimento armazenado (por hora, semanalmente, mensalmente, etc.). <br/><br/>O conjunto de dados de saída deve usar um **serviço vinculado** que se refere a um banco de dados SQL do Azure ou a um SQL data warehouse do Azure ou a um banco de dados SQL Server no qual você deseja que o procedimento armazenado seja executado. <br/><br/>O conjunto de resultados de saída pode servir como uma maneira de passar o resultado do procedimento armazenado para processamento subsequente por outra atividade ([encadeando atividades](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline) no pipeline. No entanto, Data Factory não grava automaticamente a saída de um procedimento armazenado para esse conjunto de um. É o procedimento armazenado que grava em uma tabela SQL para a qual o conjunto de resultados de saída aponta. <br/><br/>Em alguns casos, o conjunto de resultados de saída pode ser um conjunto de uma **fictício**, que é usado apenas para especificar o agendamento para executar a atividade de procedimento armazenado. |Sim |
| storedProcedureName |Especifique o nome do procedimento armazenado no banco de dados SQL do Azure ou no SQL Data Warehouse do Azure ou no banco de dados do SQL Server que é representado pelo serviço vinculado usado pela tabela de saída. |Sim |
| storedProcedureParameters |Especifique valores para parâmetros de procedimento armazenado. Se você precisar passar NULL para um parâmetro, use a sintaxe: "param1": NULL (todas as letras minúsculas). Consulte o exemplo a seguir para saber mais sobre como usar essa propriedade. |Não |

## <a name="passing-a-static-value"></a>Passando um valor estático
Agora, vamos considerar adicionar outra coluna chamada ' scenario ' na tabela que contém um valor estático chamado ' documento sample '.

![Dados de exemplo 2](./media/data-factory-stored-proc-activity/sample-data-2.png)

**Tabela**

```SQL
CREATE TABLE dbo.sampletable2
(
    Id uniqueidentifier,
    datetimestamp nvarchar(127),
    scenario nvarchar(127)
)
GO

CREATE CLUSTERED INDEX ClusteredID ON dbo.sampletable2(Id);
```

**Procedimento armazenado:**

```SQL
CREATE PROCEDURE usp_sample2 @DateTime nvarchar(127) , @Scenario nvarchar(127)

AS

BEGIN
    INSERT INTO [sampletable2]
    VALUES (newid(), @DateTime, @Scenario)
END
```

Agora, passe o parâmetro de **cenário** e o valor da atividade de procedimento armazenado. A seção **typeproperties** no exemplo anterior é semelhante ao seguinte trecho:

```JSON
"typeProperties":
{
    "storedProcedureName": "usp_sample",
    "storedProcedureParameters":
    {
        "DateTime": "$$Text.Format('{0:yyyy-MM-dd HH:mm:ss}', SliceStart)",
        "Scenario": "Document sample"
    }
}
```

**Conjunto de Data Factory:**

```JSON
{
    "name": "sprocsampleout2",
    "properties": {
        "published": false,
        "type": "AzureSqlTable",
        "linkedServiceName": "AzureSqlLinkedService",
        "typeProperties": {
            "tableName": "sampletable2"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

**Pipeline de Data Factory**

```JSON
{
    "name": "SprocActivitySamplePipeline2",
    "properties": {
        "activities": [
            {
                "type": "SqlServerStoredProcedure",
                "typeProperties": {
                    "storedProcedureName": "usp_sample2",
                    "storedProcedureParameters": {
                        "DateTime": "$$Text.Format('{0:yyyy-MM-dd HH:mm:ss}', SliceStart)",
                        "Scenario": "Document sample"
                    }
                },
                "outputs": [
                    {
                        "name": "sprocsampleout2"
                    }
                ],
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "SprocActivitySample"
            }
        ],
        "start": "2016-10-02T00:00:00Z",
        "end": "2016-10-02T05:00:00Z"
    }
}
```
