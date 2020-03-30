---
title: Atividade de procedimento armazenada no servidor SQL
description: Saiba como pode utilizar a Atividade de Procedimento Armazenada do Servidor SQL para invocar um procedimento armazenado numa base de dados Azure SQL ou no Armazém de Dados Azure SQL a partir de um pipeline data factory.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74931633"
---
# <a name="sql-server-stored-procedure-activity"></a>Atividade de procedimento armazenada no servidor SQL
> [!div class="op_single_selector" title1="Atividades de Transformação"]
> * [Atividade da Colmeia](data-factory-hive-activity.md)
> * [Atividade do Porco](data-factory-pig-activity.md)
> * [MapReduce Atividade](data-factory-map-reduce.md)
> * [Atividade de streaming de hadoop](data-factory-hadoop-streaming-activity.md)
> * [Atividade de Faísca](data-factory-spark.md)
> * [Atividade de Execução em Lote do Machine Learning](data-factory-azure-ml-batch-execution-activity.md)
> * [Atividade de Recursos de Atualização de Machine Learning](data-factory-azure-ml-update-resource-activity.md)
> * [Atividade de Procedimento Armazenado](data-factory-stored-proc-activity.md)
> * [Atividade de U-SQL do Data Lake Analytics](data-factory-usql-activity.md)
> * [.NET Atividade Personalizada](data-factory-use-custom-activities.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Azure Data Factory. Se estiver a utilizar a versão atual do serviço Data Factory, consulte [a transformação de dados utilizando a atividade de procedimento armazenado na Fábrica](../transform-data-using-stored-procedure.md)de Dados .

## <a name="overview"></a>Descrição geral
Utiliza atividades de transformação de dados num [oleoduto](data-factory-create-pipelines.md) data factory para transformar e processar dados brutos em previsões e insights. A Atividade de Procedimento Armazenado é uma das atividades de transformação que a Data Factory suporta. Este artigo baseia-se no artigo sobre atividades de transformação de [dados,](data-factory-data-transformation-activities.md) que apresenta uma visão geral da transformação de dados e das atividades de transformação apoiadas na Data Factory.

Pode utilizar a Atividade do Procedimento Armazenado para invocar um procedimento armazenado numa das seguintes lojas de dados da sua empresa ou numa máquina virtual Azure (VM):

- Base de Dados SQL do Azure
- Azure SQL Data Warehouse
- Base de dados do Servidor SQL. Se estiver a utilizar o SQL Server, instale o Portal de Gestão de Dados na mesma máquina que acolhe a base de dados ou numa máquina separada que tenha acesso à base de dados. Data Management Gateway é um componente que conecta fontes de dados no local/no Azure VM com serviços na nuvem de forma segura e gerida. Consulte o artigo gateway de [gestão](data-factory-data-management-gateway.md) de dados para mais detalhes.

> [!IMPORTANT]
> Ao copiar dados para a Base de Dados Azure SQL ou para o Servidor SQL, pode configurar o **SqlSink** na atividade de cópia para invocar um procedimento armazenado utilizando a propriedade **sqlWriterStoredProcedureName.** Para mais informações, consulte o [procedimento invocado armazenado a partir da atividade de cópia](data-factory-invoke-stored-procedure-from-copy-activity.md). Para mais detalhes sobre a propriedade, consulte os seguintes artigos de conector: [Base de Dados Azure SQL,](data-factory-azure-sql-connector.md#copy-activity-properties) [Servidor SQL](data-factory-sqlserver-connector.md#copy-activity-properties). Invocar um procedimento armazenado enquanto copia dados num Armazém de Dados Azure SQL utilizando uma atividade de cópia não é suportado. Mas pode utilizar a atividade do procedimento armazenado para invocar um procedimento armazenado num Armazém de Dados SQL.
>
> Ao copiar dados da Base de Dados Azure SQL ou do SQL Server ou do Azure SQL Data Warehouse, pode configurar o **SqlSource** na atividade de cópia para invocar um procedimento armazenado para ler dados da base de dados de origem utilizando a propriedade **SqlReaderStoredProcedureName.** Para mais informações, consulte os seguintes artigos de conector: Base de [Dados Azure SQL,](data-factory-azure-sql-connector.md#copy-activity-properties) [Servidor SQL,](data-factory-sqlserver-connector.md#copy-activity-properties)Armazém de [Dados Azure SQL](data-factory-azure-sql-data-warehouse-connector.md#copy-activity-properties)

A seguinte passagem utiliza a Atividade de Procedimento Armazenada num oleoduto para invocar um procedimento armazenado numa base de dados Azure SQL.

## <a name="walkthrough"></a>Instruções
### <a name="sample-table-and-stored-procedure"></a>Mesa de amostras e procedimento armazenado
1. Crie a **tabela** seguinte na sua Base de Dados Azure SQL utilizando o Estúdio de Gestão de Servidores SQL ou qualquer outra ferramenta com a qual esteja confortável. A coluna data-time stamp é a data e a hora em que o ID correspondente é gerado.

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
    Id é o identificado único e a coluna datatimestamp é a data e a hora em que o ID correspondente é gerado.
    
    ![Dados de exemplo](./media/data-factory-stored-proc-activity/sample-data.png)

    Nesta amostra, o procedimento armazenado encontra-se numa base de dados Azure SQL. Se o procedimento armazenado estiver num Armazém de Dados Azure SQL e na Base de Dados do Servidor SQL, a abordagem é semelhante. Para uma base de dados do Servidor SQL, tem de instalar um [Gateway de Gestão](data-factory-data-management-gateway.md)de Dados .
2. Crie o seguinte **procedimento armazenado** que insere os dados na **amostra .**

    ```SQL
    CREATE PROCEDURE usp_sample @DateTime nvarchar(127)
    AS

    BEGIN
        INSERT INTO [sampletable]
        VALUES (newid(), @DateTime)
    END
    ```

   > [!IMPORTANT]
   > **O nome** e o **invólucro** do parâmetro (DateTime neste exemplo) devem coincidir com o do parâmetro especificado no gasoduto/atividade JSON. Na definição de procedimento **\@** armazenada, certifique-se de que é utilizado como prefixo para o parâmetro.

### <a name="create-a-data-factory"></a>Criar uma fábrica de dados
1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
2. Clique em **NOVO** no menu esquerdo, clique em **Inteligência + Analytics,** e clique em **Data Factory**.

    ![Nova fábrica de dados](media/data-factory-stored-proc-activity/new-data-factory.png)
3. Na nova lâmina de fábrica de **dados,** introduza **sProcDF** para o nome. Os nomes da Azure Data Factory são **globalmente únicos.** É necessário prepor o nome da fábrica de dados com o seu nome, para permitir a criação bem sucedida da fábrica.

   ![Nova fábrica de dados](media/data-factory-stored-proc-activity/new-data-factory-blade.png)
4. Selecione a sua **subscrição Azure**.
5. Para **Grupo de Recursos**, siga um destes passos:
   1. Clique em **Criar novo** e insira um nome para o grupo de recursos.
   2. Clique **em Utilizar o existente** e selecione um grupo de recursos existente.
6. Selecione a **localização** da fábrica de dados.
7. Selecione **Pin para dashboard** para que possa ver a fábrica de dados no painel de instrumentos da próxima vez que iniciar sessão.
8. Clique em **Criar** no painel **Nova fábrica de dados**.
9. Vê-se a fábrica de dados a ser criada no painel de **instrumentos** do portal Azure. Depois de ter criado a fábrica de dados com êxito, vê a página da fábrica de dados e o respetivo conteúdo.

   ![Página inicial da Fábrica de Dados](media/data-factory-stored-proc-activity/data-factory-home-page.png)

### <a name="create-an-azure-sql-linked-service"></a>Criar um serviço ligado ao Azure SQL
Depois de criar a fábrica de dados, cria um serviço ligado ao Azure SQL que liga a sua base de dados Azure SQL, que contém a tabela de amostras e usp_sample procedimento armazenado, à sua fábrica de dados.

1. Clique em **Autor e implante** na lâmina data **Factory** para **a SProcDF** para lançar o Data Factory Editor.
2. Clique em **Nova loja** de dados na barra de comandoe escolha base de **dados Azure SQL**. Você deve ver o script JSON para criar um serviço ligado Azure SQL no editor.

   ![Nova loja de dados](media/data-factory-stored-proc-activity/new-data-store.png)
3. No script JSON, faça as seguintes alterações:

   1. Substitua-o `<servername>` pelo nome do seu servidor de base de dados Azure SQL.
   2. Substitua-a `<databasename>` pela base de dados em que criou a tabela e o procedimento armazenado.
   3. Substitua-a `<username@servername>` pela conta de utilizador que tem acesso à base de dados.
   4. Substitua-a `<password>` com a palavra-passe para a conta de utilizador.

      ![Nova loja de dados](media/data-factory-stored-proc-activity/azure-sql-linked-service.png)
4. Para implementar o serviço ligado, clique em **Implementar** na barra de comando. Confirme que vê o AzureSqlLinkedService na vista da árvore à esquerda.

    ![vista de árvore com serviço ligado](media/data-factory-stored-proc-activity/tree-view.png)

### <a name="create-an-output-dataset"></a>Criar um conjunto de dados de saída
Deve especificar um conjunto de dados de saída para uma atividade de procedimento armazenado, mesmo que o procedimento armazenado não produza quaisquer dados. Isto porque é o conjunto de dados de saída que impulsiona o horário da atividade (com que frequência a atividade é executada - hora, diariamente, etc.). O conjunto de dados de saída deve utilizar um **serviço ligado** que se refira a uma Base de Dados Azure SQL ou a um Armazém de Dados Azure SQL ou a uma Base de Dados de Servidores SQL em que pretende que o procedimento armazenado seja executado. O conjunto de dados de saída pode servir como forma de passar o resultado do procedimento armazenado para posterior processamento por outra atividade[(atividades de acorrentação](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline) no gasoduto. No entanto, a Data Factory não escreve automaticamente a saída de um procedimento armazenado para este conjunto de dados. É o procedimento armazenado que escreve para uma tabela SQL que o conjunto de dados de saída aponta. Em alguns casos, o conjunto de dados de saída pode ser um conjunto de **dados manequim** (um conjunto de dados que aponta para uma tabela que realmente não detém a saída do procedimento armazenado). Este conjunto de dados de boneco é utilizado apenas para especificar o horário para executar a atividade do procedimento armazenado.

1. **Clique... Mais** na barra de ferramentas, clique em Novo conjunto de **dados,** e clique em **Azure SQL**. **Novo conjunto de dados** na barra de comando e selecione **Azure SQL**.

    ![vista de árvore com serviço ligado](media/data-factory-stored-proc-activity/new-dataset.png)
2. Copie/cole o seguinte guião JSON no editor da JSON.

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
3. Para implementar o conjunto de dados, clique em **Implementar** na barra de comando. Confirme que vê o conjunto de dados na vista da árvore.

    ![vista de árvore com serviços ligados](media/data-factory-stored-proc-activity/tree-view-2.png)

### <a name="create-a-pipeline-with-sqlserverstoredprocedure-activity"></a>Criar um pipeline com atividade de Procedimento SqlServerStoredProcedure
Agora, vamos criar um oleoduto com uma atividade de procedimento armazenada.

Note as seguintes propriedades:

- A propriedade **do tipo** está definida para **SqlServerStoredProcedure**.
- O nome do tipo armazenado Procedimento **sé** definido para **usp_sample** (nome do procedimento armazenado).
- A secção **Deparadores de Procedimentos armazenados** contém um parâmetro chamado **DateTime**. O nome e o invólucro do parâmetro em JSON devem coincidir com o nome e o invólucro do parâmetro na definição de procedimento armazenado. Se precisar de passar nulo para um parâmetro, `"param1": null` utilize a sintaxe: (todas as minúsculas).

1. **Clique... Mais** na barra de comando e clique em **Novo oleoduto**.
2. Copiar/colar o seguinte corte JSON:

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
3. Para implantar o gasoduto, clique em **colocar** na barra de ferramentas.

### <a name="monitor-the-pipeline"></a>Monitorizar o pipeline
1. Clique em **X** para fechar os painéis do Editor do Data Factory e para regressar ao painel Data Factory e, em seguida, clique em **Diagrama**.

    ![figura de azulejo](media/data-factory-stored-proc-activity/data-factory-diagram-tile.png)
2. Na vista do **diagrama,** você vê uma visão geral dos oleodutos, e conjuntos de dados usados neste tutorial.

    ![figura de azulejo](media/data-factory-stored-proc-activity/data-factory-diagram-view.png)
3. Na vista do diagrama, clique `sprocsampleout`duas vezes no conjunto de dados . Vê as fatias em estado pronto. Deve haver cinco fatias porque uma fatia é produzida por cada hora entre a hora de início e o tempo final do JSON.

    ![figura de azulejo](media/data-factory-stored-proc-activity/data-factory-slices.png)
4. Quando uma fatia estiver em `select * from sampletable` estado **ready,** eexecute uma consulta contra a base de dados Azure SQL para verificar se os dados foram inseridos na tabela pelo procedimento armazenado.

   ![Dados de saída](./media/data-factory-stored-proc-activity/output.png)

   Consulte [o gasoduto](data-factory-monitor-manage-pipelines.md) para obter informações detalhadas sobre a monitorização dos gasodutos da Fábrica de Dados Azure.

## <a name="specify-an-input-dataset"></a>Especificar um conjunto de dados de entrada
No walkthrough, a atividade do procedimento armazenado não tem quaisquer conjuntos de dados de entrada. Se especificar um conjunto de dados de entrada, a atividade do procedimento armazenado não funciona até que a fatia do conjunto de dados de entrada esteja disponível (em estado de pronto). O conjunto de dados pode ser um conjunto de dados externo (que não é produzido por outra atividade no mesmo pipeline) ou um conjunto de dados interno que é produzido por uma atividade a montante (a atividade que funciona antes desta atividade). Pode especificar vários conjuntos de dados de entrada para a atividade do procedimento armazenado. Se o fizer, a atividade do procedimento armazenado só funciona quando todas as fatias de conjunto de dados de entrada estiverem disponíveis (em estado ready). O conjunto de dados de entrada não pode ser consumido no procedimento armazenado como parâmetro. É utilizado apenas para verificar a dependência antes de iniciar a atividade do procedimento armazenado.

## <a name="chaining-with-other-activities"></a>Acorrentar com outras atividades
Se pretender acorrentar uma atividade a montante com esta atividade, especifique a saída da atividade a montante como uma entrada desta atividade. Quando o fizer, a atividade do procedimento armazenado não funciona até que a atividade a montante esteja concluída e o conjunto de dados de saída da atividade a montante esteja disponível (em estado de Ready). Pode especificar conjuntos de dados de saída de múltiplas atividades a montante como conjuntos de dados de entrada da atividade do procedimento armazenado. Quando o fizer, a atividade do procedimento armazenado só funciona quando todas as fatias de conjunto de dados de entrada estiverem disponíveis.

No exemplo seguinte, a saída da atividade da cópia é: OutputDataset, que é uma entrada da atividade do procedimento armazenado. Por isso, a atividade do procedimento armazenado não funciona até que a atividade de cópia esteja concluída e a fatia OutputDataset esteja disponível (em estado ready). Se especificar vários conjuntos de dados de entrada, a atividade do procedimento armazenado não funciona até que todas as fatias de conjunto de dados de entrada estejam disponíveis (em estado ready). Os conjuntos de dados de entrada não podem ser utilizados diretamente como parâmetros para a atividade do procedimento armazenado.

Para obter mais informações sobre atividades de cadeia, consulte [múltiplas atividades num oleoduto](data-factory-create-pipelines.md#multiple-activities-in-a-pipeline)

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

Da mesma forma, para associar a atividade do procedimento da loja às atividades a **jusante** (as atividades que decorrem após o encerramento da atividade do procedimento armazenado), especificar o conjunto de dados de saída da atividade do procedimento armazenado como entrada da atividade a jusante no gasoduto.

> [!IMPORTANT]
> Ao copiar dados para a Base de Dados Azure SQL ou para o Servidor SQL, pode configurar o **SqlSink** na atividade de cópia para invocar um procedimento armazenado utilizando a propriedade **sqlWriterStoredProcedureName.** Para mais informações, consulte o [procedimento invocado armazenado a partir da atividade de cópia](data-factory-invoke-stored-procedure-from-copy-activity.md). Para mais detalhes sobre a propriedade, consulte os seguintes artigos de conector: [Base de Dados Azure SQL,](data-factory-azure-sql-connector.md#copy-activity-properties) [Servidor SQL](data-factory-sqlserver-connector.md#copy-activity-properties).
> 
> Ao copiar dados da Base de Dados Azure SQL ou do SQL Server ou do Azure SQL Data Warehouse, pode configurar o **SqlSource** na atividade de cópia para invocar um procedimento armazenado para ler dados da base de dados de origem utilizando a propriedade **SqlReaderStoredProcedureName.** Para mais informações, consulte os seguintes artigos de conector: Base de [Dados Azure SQL,](data-factory-azure-sql-connector.md#copy-activity-properties) [Servidor SQL,](data-factory-sqlserver-connector.md#copy-activity-properties)Armazém de [Dados Azure SQL](data-factory-azure-sql-data-warehouse-connector.md#copy-activity-properties)

## <a name="json-format"></a>Formato JSON
Aqui está o formato JSON para definir uma Atividade de Procedimento Armazenada:

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
| descrição |Texto descrevendo para que a atividade é usada |Não |
| tipo | Deve ser definido para: **SqlServerStoredProcedure** | Sim |
| inputs | Opcional. Se especificar um conjunto de dados de entrada, este deve estar disponível (em estado 'Pronto') para que a atividade do procedimento armazenado seja executada. O conjunto de dados de entrada não pode ser consumido no procedimento armazenado como parâmetro. É utilizado apenas para verificar a dependência antes de iniciar a atividade do procedimento armazenado. |Não |
| saídas | Deve especificar um conjunto de dados de saída para uma atividade de procedimento armazenado. O conjunto de dados de saída especifica o **calendário** para a atividade do procedimento armazenado (hora, semanal, mensal, etc.). <br/><br/>O conjunto de dados de saída deve utilizar um **serviço ligado** que se refira a uma Base de Dados Azure SQL ou a um Armazém de Dados Azure SQL ou a uma Base de Dados de Servidores SQL em que pretende que o procedimento armazenado seja executado. <br/><br/>O conjunto de dados de saída pode servir como forma de passar o resultado do procedimento armazenado para posterior processamento por outra atividade[(atividades de acorrentação](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline) no gasoduto. No entanto, a Data Factory não escreve automaticamente a saída de um procedimento armazenado para este conjunto de dados. É o procedimento armazenado que escreve para uma tabela SQL que o conjunto de dados de saída aponta. <br/><br/>Em alguns casos, o conjunto de dados de saída pode ser um conjunto de **dados de bonecos,** que é usado apenas para especificar o horário para executar a atividade do procedimento armazenado. |Sim |
| nome de procedimento armazenado |Especifique o nome do procedimento armazenado na base de dados Azure SQL ou no Azure SQL Data Warehouse ou na base de dados SQL Server que está representado pelo serviço ligado que a tabela de saída utiliza. |Sim |
| parâmetros de procedimento saqueados |Especifique os valores dos parâmetros de procedimento armazenados. Se precisar de passar nulo por um parâmetro, utilize a sintaxe: "param1": nulo (todos os casos inferiores). Consulte a seguinte amostra para aprender sobre a utilização desta propriedade. |Não |

## <a name="passing-a-static-value"></a>Passando um valor estático
Agora, vamos considerar adicionar outra coluna chamada "Cenário" na tabela contendo um valor estático chamado "Amostra de documento".

![Dados da amostra 2](./media/data-factory-stored-proc-activity/sample-data-2.png)

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

Agora, passe o parâmetro **cenário** e o valor da atividade do procedimento armazenado. A secção **typeProperties** na amostra anterior parece ser o seguinte corte:

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

**Conjunto de dados da Fábrica de Dados:**

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

**Gasoduto data factory**

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
