---
title: Atividade de procedimento armazenado do SQL Server
description: Saiba como pode utilizar a atividade de procedimento armazenado do SQL Server para invocar um procedimento armazenado num banco de dados do Azure SQL ou o Azure SQL Data Warehouse a partir de um pipeline do Data Factory.
services: data-factory
documentationcenter: ''
ms.assetid: 1c46ed69-4049-44ec-9b46-e90e964a4a8e
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
author: nabhishek
ms.author: abnarain
manager: craigg
robots: noindex
ms.openlocfilehash: 77842b60108629168f423f25eb03b01079cf55e5
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57775362"
---
# <a name="sql-server-stored-procedure-activity"></a>Atividade de procedimento armazenado do SQL Server
> [!div class="op_single_selector" title1="Transformation Activities"]
> * [Atividade do Hive](data-factory-hive-activity.md)
> * [Atividade PIg](data-factory-pig-activity.md)
> * [Atividade MapReduce](data-factory-map-reduce.md)
> * [Atividade de transmissão em fluxo do Hadoop](data-factory-hadoop-streaming-activity.md)
> * [Atividade do Spark](data-factory-spark.md)
> * [Atividade de Execução em Lote do Machine Learning](data-factory-azure-ml-batch-execution-activity.md)
> * [Atividade de Recursos de Atualização de Machine Learning](data-factory-azure-ml-update-resource-activity.md)
> * [Atividade de Procedimento Armazenado](data-factory-stored-proc-activity.md)
> * [Atividade de U-SQL do Data Lake Analytics](data-factory-usql-activity.md)
> * [Atividade personalizada do .NET](data-factory-use-custom-activities.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Azure Data Factory. Se estiver a utilizar a versão atual do serviço Data Factory, veja [transformar dados com a atividade de procedimento armazenado no Data Factory](../transform-data-using-stored-procedure.md).

## <a name="overview"></a>Descrição geral
Utilize atividades de transformação de dados numa fábrica de dados [pipeline](data-factory-create-pipelines.md) para transformar e processar dados não processados em predições e informações. A atividade de procedimento armazenado é uma das atividades de transformação que o Data Factory suporta. Este artigo baseia-se a [atividades de transformação de dados](data-factory-data-transformation-activities.md) artigo, que apresenta uma visão geral de transformação de dados e as atividades de transformação suportados no Data Factory.

Pode usar a atividade de procedimento armazenado para invocar um procedimento armazenado em um dos seguintes arquivos de dados na sua empresa ou numa máquina virtual do Azure (VM):

- Base de Dados SQL do Azure
- Azure SQL Data Warehouse
- Base de dados do SQL Server. Se estiver a utilizar o SQL Server, instale o Data Management Gateway no mesmo computador que aloja a base de dados ou num computador separado que tenha acesso à base de dados. O Data Management Gateway é um componente que liga os dados de origens no local/na VM do Azure com os serviços cloud de forma segura e gerida. Ver [Data Management Gateway](data-factory-data-management-gateway.md) artigo para obter detalhes.

> [!IMPORTANT]
> Quando se copiam dados na base de dados do Azure SQL ou SQL Server, pode configurar o **SqlSink** na atividade de cópia para invocar um procedimento armazenado utilizando o **sqlWriterStoredProcedureName** propriedade. Para obter mais informações, consulte [invocar um procedimento armazenado de atividade de cópia](data-factory-invoke-stored-procedure-from-copy-activity.md). Para obter detalhes sobre a propriedade, veja a seguir os artigos de conector: [Base de dados SQL do Azure](data-factory-azure-sql-connector.md#copy-activity-properties), [do SQL Server](data-factory-sqlserver-connector.md#copy-activity-properties). Invocar um procedimento armazenado ao copiar dados para um Azure SQL Data Warehouse, utilizando uma atividade de cópia não é suportada. No entanto, pode usar a atividade de procedimento armazenado para invocar um procedimento armazenado num SQL Data Warehouse.
>
> Quando se copiam dados a partir da base de dados do Azure SQL ou SQL Server ou o Azure SQL Data Warehouse, pode configurar **SqlSource** na atividade de cópia para invocar um procedimento armazenado para ler dados da base de dados de origem com o  **sqlReaderStoredProcedureName** propriedade. Para obter mais informações, consulte os seguintes artigos de conector: [Base de dados SQL do Azure](data-factory-azure-sql-connector.md#copy-activity-properties), [do SQL Server](data-factory-sqlserver-connector.md#copy-activity-properties), [armazém de dados SQL do Azure](data-factory-azure-sql-data-warehouse-connector.md#copy-activity-properties)

A instrução a seguir usa a atividade de procedimento armazenado num pipeline para invocar um procedimento armazenado numa base de dados SQL do Azure.

## <a name="walkthrough"></a>Instruções
### <a name="sample-table-and-stored-procedure"></a>Tabela de exemplo e o procedimento armazenado
1. Crie as seguintes **tabela** na sua base de dados de SQL do Azure com o SQL Server Management Studio ou qualquer outra ferramenta que se sente confortável. A coluna de carimbodedataehora é a data e hora quando é gerado o ID correspondente.

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
    O ID é identificado exclusivo e a coluna de carimbodedataehora é a data e hora quando é gerado o ID correspondente.
    
    ![Dados de exemplo](./media/data-factory-stored-proc-activity/sample-data.png)

    Neste exemplo, é o procedimento armazenado numa base de dados SQL do Azure. Se o procedimento armazenado num Azure SQL Data Warehouse e a base de dados do SQL Server, a abordagem é semelhante. Para uma base de dados do SQL Server, tem de instalar um [Data Management Gateway](data-factory-data-management-gateway.md).
2. Crie as seguintes **procedimento armazenado** que insere dados para o **sampletable**.

    ```SQL
    CREATE PROCEDURE usp_sample @DateTime nvarchar(127)
    AS

    BEGIN
        INSERT INTO [sampletable]
        VALUES (newid(), @DateTime)
    END
    ```

   > [!IMPORTANT]
   > **Nome** e **letras maiúsculas e minúsculas** do parâmetro (DateTime neste exemplo) tem de corresponder do parâmetro especificado no pipeline/JSON de atividade. Na definição do procedimento armazenado, certifique-se de que **\@** é utilizado como um prefixo para o parâmetro.

### <a name="create-a-data-factory"></a>Criar uma fábrica de dados
1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
2. Clique em **NEW** no menu da esquerda, clique em **inteligência + análise**e clique em **Data Factory**.

    ![Nova fábrica de dados](media/data-factory-stored-proc-activity/new-data-factory.png)
3. Na **nova fábrica de dados** painel, introduza **SProcDF** para o nome. Os nomes de fábrica de dados do Azure são **globalmente exclusivo**. Terá de preceder o nome da fábrica de dados com seu nome, para permitir a criação bem-sucedida da fábrica.

   ![Nova fábrica de dados](media/data-factory-stored-proc-activity/new-data-factory-blade.png)
4. Selecione a sua **subscrição do Azure**.
5. Para **Grupo de Recursos**, siga um destes passos:
   1. Clique em **criar novo** e introduza um nome para o grupo de recursos.
   2. Clique em **utilizar existente** e selecione um grupo de recursos existente.
6. Selecione a **localização** da fábrica de dados.
7. Selecione **afixar ao dashboard** para que possa ver a fábrica de dados no dashboard próxima vez que iniciar sessão.
8. Clique em **Criar** no painel **Nova fábrica de dados**.
9. Verá a fábrica de dados que está a ser criada no **dashboard** do portal do Azure. Depois de ter criado a fábrica de dados com êxito, vê a página da fábrica de dados e o respetivo conteúdo.

   ![Home page do Data Factory](media/data-factory-stored-proc-activity/data-factory-home-page.png)

### <a name="create-an-azure-sql-linked-service"></a>Criar um serviço ligado SQL do Azure
Depois de criar a fábrica de dados, criar um SQL do Azure serviço ligado que liga a sua base de dados SQL do Azure, que contém a tabela de sampletable e usp_sample armazenados procedimento, à fábrica de dados.

1. Clique em **autor e implementar** sobre o **Data Factory** painel **SProcDF** para iniciar o Editor do Data Factory.
2. Clique em **novo arquivo de dados** sobre o comando da barra e escolha **base de dados do Azure SQL**. Deverá ver o script JSON para criar um serviço ligado SQL do Azure no editor.

   ![Novo arquivo de dados](media/data-factory-stored-proc-activity/new-data-store.png)
3. No script de JSON, efetue as seguintes alterações:

   1. Substitua `<servername>` com o nome do seu servidor de base de dados do Azure SQL.
   2. Substitua `<databasename>` com a base de dados em que criou a tabela e o procedimento armazenado.
   3. Substitua `<username@servername>` com a conta de utilizador que tenha acesso à base de dados.
   4. Substitua `<password>` com a palavra-passe da conta de utilizador.

      ![Novo arquivo de dados](media/data-factory-stored-proc-activity/azure-sql-linked-service.png)
4. Para implementar o serviço ligado, clique em **Deploy** na barra de comandos. Confirme que vê o AzureSqlLinkedService na vista de árvore à esquerda.

    ![Vista de árvore com o serviço ligado](media/data-factory-stored-proc-activity/tree-view.png)

### <a name="create-an-output-dataset"></a>Criar um conjunto de dados de saída
Tem de especificar um conjunto de dados de saída para uma atividade de procedimento armazenado, mesmo que o procedimento armazenado não produz quaisquer dados. Isso ocorre porque é o conjunto de dados de saída que controla a agenda da atividade (a frequência com que a atividade é executada - hora a hora, diariamente, etc.). O conjunto de dados de saída tem de utilizar um **serviço ligado** que se refere a uma base de dados do SQL do Azure ou um Azure SQL Data Warehouse ou uma base de dados SQL Server em que pretenda que o procedimento armazenado a executar. O conjunto de dados de saída pode servir como uma forma de transmitir o resultado do procedimento armazenado para processamento por outra atividade subsequente ([encadear atividades](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline) no pipeline. No entanto, a fábrica de dados não automaticamente escrever a saída de um procedimento armazenado para este conjunto de dados. É o procedimento armazenado que escreve para uma tabela SQL que o conjunto de dados de saída aponta para. Em alguns casos, o conjunto de dados de saída pode ser um **conjunto de dados fictício** (um conjunto de dados que aponta para uma tabela que não realmente contém a saída do procedimento armazenado). Este conjunto de dados fictício é utilizado apenas para especificar a agenda para executar a atividade de procedimento armazenado.

1. Clique em **... Obter mais** na barra de ferramentas, clique em **novo conjunto de dados**e clique em **SQL do Azure**. **Novo conjunto de dados** na barra de comandos e selecione **SQL do Azure**.

    ![Vista de árvore com o serviço ligado](media/data-factory-stored-proc-activity/new-dataset.png)
2. Copiar/colar o seguinte script JSON para o editor de JSON.

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
3. Para implementar o conjunto de dados, clique em **Deploy** na barra de comandos. Confirme que vê o conjunto de dados na vista de árvore.

    ![Vista de árvore com serviços ligados](media/data-factory-stored-proc-activity/tree-view-2.png)

### <a name="create-a-pipeline-with-sqlserverstoredprocedure-activity"></a>Criar um pipeline com atividade SqlServerStoredProcedure
Agora, vamos criar um pipeline com uma atividade de procedimento armazenado.

Tenha em atenção as seguintes propriedades:

- O **tipo** estiver definida como **SqlServerStoredProcedure**.
- O **storedProcedureName** propriedades do tipo está definido como **usp_sample** (nome do procedimento armazenado).
- O **storedProcedureParameters** secção contém um parâmetro com o nome **DateTime**. Nome e tem maiúsculas e minúsculas do parâmetro no JSON têm de corresponder o nome e o parâmetro na definição do procedimento armazenado letras maiúsculas e minúsculas. Se precisar passar nulo para um parâmetro, utilize a sintaxe: `"param1": null` (em minúsculas).

1. Clique em **... Obter mais** na barra de comandos e clique em **novo pipeline**.
2. Copiar/colar o fragmento JSON seguinte:

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
3. Para implementar o pipeline, clique em **Deploy** na barra de ferramentas.

### <a name="monitor-the-pipeline"></a>Monitorizar o pipeline
1. Clique em **X** para fechar os painéis do Editor do Data Factory e para regressar ao painel Data Factory e, em seguida, clique em **Diagrama**.

    ![Mosaico do diagrama](media/data-factory-stored-proc-activity/data-factory-diagram-tile.png)
2. Na **Vista de Diagrama**, verá uma descrição geral dos pipelines e dos conjuntos de dados utilizados neste tutorial.

    ![Mosaico do diagrama](media/data-factory-stored-proc-activity/data-factory-diagram-view.png)
3. Na vista de diagrama, faça duplo clique no conjunto de dados `sprocsampleout`. Ver os setores no estado pronto. Deve haver cinco setores porque é produzido um setor para cada hora entre a hora de início e hora de fim a partir do JSON.

    ![Mosaico do diagrama](media/data-factory-stored-proc-activity/data-factory-slices.png)
4. Quando um setor estiver no **pronto** Estado, execute um `select * from sampletable` consulta na base de dados SQL do Azure para verificar que os dados foi inseridos na tabela pelo procedimento armazenado.

   ![Dados de saída](./media/data-factory-stored-proc-activity/output.png)

   Ver [monitorizar o pipeline](data-factory-monitor-manage-pipelines.md) para obter informações detalhadas sobre a monitorização de pipelines do Azure Data Factory.

## <a name="specify-an-input-dataset"></a>Especifique um conjunto de dados de entrada
No passo a passo, a atividade de procedimento armazenado não tem quaisquer conjuntos de dados de entrada. Se especificar um conjunto de dados de entrada, a atividade de procedimento armazenado não é executado até que o setor de conjunto de dados de entrada está disponível (no estado pronto). O conjunto de dados pode ser um conjunto de dados externo (que não é produzido pelo outra atividade no mesmo pipeline) ou um conjunto de dados interno que é produzido por uma atividade a montante (a atividade que é executado antes desta atividade). É possível especificar vários conjuntos de dados de entrada para a atividade de procedimento armazenado. Se fizer isso, a atividade de procedimento armazenado é executado apenas quando todos os setores do conjunto de dados de entrada estão disponíveis (no estado pronto). O conjunto de dados de entrada não pode ser consumido no procedimento armazenado como um parâmetro. Só é utilizado para verificar a dependência antes de iniciar a atividade de procedimento armazenado.

## <a name="chaining-with-other-activities"></a>O encadeamento com outras atividades
Se quiser encadear uma atividade a montante com esta atividade, especifique a saída da atividade a montante como entrada dessa atividade. Ao fazê-lo, a atividade de procedimento armazenado não é executado até que a atividade a montante esteja concluída e o conjunto de dados de saída da atividade a montante está disponível (no estado pronto). Pode especificar os conjuntos de dados de saída de várias atividades a montante como conjuntos de dados de entrada da atividade de procedimento armazenado. Ao fazê-lo, a atividade de procedimento armazenado é executado apenas quando todos os setores do conjunto de dados de entrada estão disponíveis.

No exemplo seguinte, a saída da atividade de cópia é: OutputDataset, que é uma entrada da atividade de procedimento armazenado. Por conseguinte, a atividade de procedimento armazenado não é executado até concluir a atividade de cópia e o setor de OutputDataset está disponível (no estado pronto). Se especificar vários conjuntos de dados de entrada, a atividade de procedimento armazenado não é executado até que todos os setores de entrada do conjunto de dados estejam disponíveis (no estado pronto). Os conjuntos de dados de entrada não podem ser utilizados diretamente como parâmetros para a atividade de procedimento armazenado.

Para obter mais informações sobre o encadeamento de atividades, consulte [múltiplas atividades num pipeline](data-factory-create-pipelines.md#multiple-activities-in-a-pipeline)

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

Da mesma forma, para ligar a atividade de procedimento de arquivo com **atividades downstream** (as atividades que são executadas depois de concluir a atividade de procedimento armazenado), especifique o conjunto de dados de saída da atividade de procedimento armazenado como uma entrada do atividade de Downstream no pipeline.

> [!IMPORTANT]
> Quando se copiam dados na base de dados do Azure SQL ou SQL Server, pode configurar o **SqlSink** na atividade de cópia para invocar um procedimento armazenado utilizando o **sqlWriterStoredProcedureName** propriedade. Para obter mais informações, consulte [invocar um procedimento armazenado de atividade de cópia](data-factory-invoke-stored-procedure-from-copy-activity.md). Para obter detalhes sobre a propriedade, consulte os seguintes artigos de conector: [Base de dados SQL do Azure](data-factory-azure-sql-connector.md#copy-activity-properties), [do SQL Server](data-factory-sqlserver-connector.md#copy-activity-properties).
> 
> Quando se copiam dados a partir da base de dados do Azure SQL ou SQL Server ou o Azure SQL Data Warehouse, pode configurar **SqlSource** na atividade de cópia para invocar um procedimento armazenado para ler dados da base de dados de origem com o  **sqlReaderStoredProcedureName** propriedade. Para obter mais informações, consulte os seguintes artigos de conector: [Base de dados SQL do Azure](data-factory-azure-sql-connector.md#copy-activity-properties), [do SQL Server](data-factory-sqlserver-connector.md#copy-activity-properties), [armazém de dados SQL do Azure](data-factory-azure-sql-data-warehouse-connector.md#copy-activity-properties)

## <a name="json-format"></a>Formato JSON
Este é o formato JSON para a definição de uma atividade de procedimento armazenado:

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

A tabela seguinte descreve estas propriedades JSON:

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| nome | Nome da atividade |Sim |
| descrição |Texto que descreve o que a atividade é utilizada para |Não |
| tipo | Deve ser definida como: **SqlServerStoredProcedure** | Sim |
| entradas | Opcional. Se especificar um conjunto de dados de entrada, tem de estar disponível (no estado "Pronto") para a atividade de procedimento armazenado ser executado. O conjunto de dados de entrada não pode ser consumido no procedimento armazenado como um parâmetro. Só é utilizado para verificar a dependência antes de iniciar a atividade de procedimento armazenado. |Não |
| saídas | Tem de especificar um conjunto de dados de saída para uma atividade de procedimento armazenado. Conjunto de dados de saída especifica a **agenda** para a atividade de procedimento armazenado (hora a hora, semanalmente, mensalmente, etc.). <br/><br/>O conjunto de dados de saída tem de utilizar um **serviço ligado** que se refere a uma base de dados do SQL do Azure ou um Azure SQL Data Warehouse ou uma base de dados SQL Server em que pretenda que o procedimento armazenado a executar. <br/><br/>O conjunto de dados de saída pode servir como uma forma de transmitir o resultado do procedimento armazenado para processamento por outra atividade subsequente ([encadear atividades](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline) no pipeline. No entanto, a fábrica de dados não automaticamente escrever a saída de um procedimento armazenado para este conjunto de dados. É o procedimento armazenado que escreve para uma tabela SQL que o conjunto de dados de saída aponta para. <br/><br/>Em alguns casos, o conjunto de dados de saída pode ser um **conjunto de dados fictício**, que é utilizado apenas para especificar a agenda para executar a atividade de procedimento armazenado. |Sim |
| storedProcedureName |Especifique o nome do procedimento armazenado na base de dados SQL do Azure ou base de dados Azure SQL Data Warehouse ou SQL Server que é representado pelo serviço ligado que utilize a tabela de saída. |Sim |
| storedProcedureParameters |Especifique os valores dos parâmetros do procedimento armazenado. Se precisar de passar nulo para um parâmetro, utilize a sintaxe: "param1": null (todas as letras minúsculas). Veja o exemplo seguinte para saber como utilizar esta propriedade. |Não |

## <a name="passing-a-static-value"></a>Passar um valor estático
Agora, vamos considerar adicionar outra coluna com o nome 'Cenário' na tabela que contém um valor estático chamado "Exemplo de documentos".

![Dados de exemplo 2](./media/data-factory-stored-proc-activity/sample-data-2.png)

**Tabela:**

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

Agora, passar o **cenário** parâmetro e o valor da atividade de procedimento armazenado. O **typeProperties** secção no exemplo anterior mostra como o seguinte fragmento:

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

**Conjunto de dados do Data Factory:**

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

**Pipeline de fábrica de dados**

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
