---
title: Atividade do procedimento armazenado do servidor SQL
description: Saiba como pode utilizar a Atividade de Procedimento Armazenada do Servidor SQL para invocar um procedimento armazenado numa Base de Dados Azure SQL ou Azure Synapse Analytics a partir de um oleoduto de Fábrica de Dados.
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
author: nabhishek
ms.author: abnarain
robots: noindex
ms.openlocfilehash: 05717352936bed888e108277d0163e43bc5a37af
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100368766"
---
# <a name="sql-server-stored-procedure-activity"></a>Atividade do procedimento armazenado do servidor SQL
> [!div class="op_single_selector" title1="Atividades de Transformação"]
> * [Atividade da Colmeia](data-factory-hive-activity.md)
> * [Atividade do Porco](data-factory-pig-activity.md)
> * [Atividade mapReduce](data-factory-map-reduce.md)
> * [Atividade de streaming de Hadoop](data-factory-hadoop-streaming-activity.md)
> * [Atividade de Faísca](data-factory-spark.md)
> * [Atividade de Execução em Lotes do Azure Machine Learning Studio (clássico)](data-factory-azure-ml-batch-execution-activity.md)
> * [Atividade de Recursos de Atualização do Azure Machine Learning Studio (clássico)](data-factory-azure-ml-update-resource-activity.md)
> * [Atividade de Procedimento Armazenado](data-factory-stored-proc-activity.md)
> * [Atividade de U-SQL do Data Lake Analytics](data-factory-usql-activity.md)
> * [.NET Atividade Personalizada](data-factory-use-custom-activities.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Azure Data Factory. Se estiver a utilizar a versão atual do serviço Data Factory, consulte os dados de [transformação utilizando a atividade de procedimento armazenado na Data Factory](../transform-data-using-stored-procedure.md).

## <a name="overview"></a>Descrição Geral
Utiliza atividades de transformação de dados num [oleoduto](data-factory-create-pipelines.md) da Data Factory para transformar e processar dados brutos em previsões e insights. A Atividade procedimentos armazenadas é uma das atividades de transformação que a Data Factory suporta. Este artigo baseia-se no artigo de atividades de [transformação](data-factory-data-transformation-activities.md) de dados, que apresenta uma visão geral da transformação de dados e das atividades de transformação apoiadas na Data Factory.

Pode utilizar a Atividade De Procedimento Armazenada para invocar um procedimento armazenado numa das seguintes lojas de dados na sua empresa ou numa máquina virtual Azure (VM):

- Base de Dados SQL do Azure
- Azure Synapse Analytics
- Base de Dados do Servidor SQL. Se estiver a utilizar o SQL Server, instale o Data Management Gateway na mesma máquina que acolhe a base de dados ou numa máquina separada que tenha acesso à base de dados. Data Management Gateway é um componente que liga fontes de dados no local/em Azure VM com serviços na nuvem de forma segura e gerida. Consulte o artigo [data Management Gateway](data-factory-data-management-gateway.md) para mais detalhes.

> [!IMPORTANT]
> Ao copiar dados para a Base de Dados Azure SQL ou para o SQL Server, pode configurar o **SqlSink** na atividade de cópia para invocar um procedimento armazenado utilizando a propriedade **sqlWriterStorDProcedureName.** Para obter mais informações, consulte [invocar o procedimento armazenado a partir da atividade de cópia](data-factory-invoke-stored-procedure-from-copy-activity.md). Para mais informações sobre a propriedade, consulte os seguintes artigos de conector: [Azure SQL Database,](data-factory-azure-sql-connector.md#copy-activity-properties) [SQL Server](data-factory-sqlserver-connector.md#copy-activity-properties). Invocar um procedimento armazenado ao copiar dados para a Azure Synapse Analytics utilizando uma atividade de cópia não é suportado. Mas, você pode usar a atividade de procedimento armazenado para invocar um procedimento armazenado em Azure Synapse Analytics.
>
> Ao copiar dados da Base de Dados Azure SQL ou do SQL Server ou do Azure Synapse Analytics, pode configurar **o SqlSource** na atividade de cópia para invocar um procedimento armazenado para ler dados a partir da base de dados de origem utilizando a propriedade **sqlReaderStoredProcedureName.** Para mais informações, consulte os seguintes artigos de conector: [Azure SQL Database](data-factory-azure-sql-connector.md#copy-activity-properties), [SQL Server](data-factory-sqlserver-connector.md#copy-activity-properties), [Azure Synapse Analytics](data-factory-azure-sql-data-warehouse-connector.md#copy-activity-properties)

A seguinte passagem utiliza a Atividade de Procedimento Armazenado num oleoduto para invocar um procedimento armazenado na Base de Dados Azure SQL.

## <a name="walkthrough"></a>Instruções
### <a name="sample-table-and-stored-procedure"></a>Quadro de amostras e procedimento armazenado
1. Crie a **seguinte tabela** na sua Base de Dados Azure SQL utilizando o SQL Server Management Studio ou qualquer outra ferramenta com a quais se sinta confortável. A coluna de datatimestamp é a data e a hora em que o ID correspondente é gerado.

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
    Id é o único identificado e a coluna de datatimestamp é a data e a hora em que o ID correspondente é gerado.
    
    ![Dados de exemplo](./media/data-factory-stored-proc-activity/sample-data.png)

    Nesta amostra, o procedimento armazenado encontra-se numa Base de Dados Azure SQL. Se o procedimento armazenado estiver no Azure Synapse Analytics e na SQL Server Database, a abordagem é semelhante. Para uma base de dados SQL Server, tem de instalar um [Gateway de Gestão de Dados](data-factory-data-management-gateway.md).
2. Crie o seguinte **procedimento armazenado** que insere os dados na **amostragem**.

    ```SQL
    CREATE PROCEDURE usp_sample @DateTime nvarchar(127)
    AS

    BEGIN
        INSERT INTO [sampletable]
        VALUES (newid(), @DateTime)
    END
    ```

   > [!IMPORTANT]
   > **O nome** e **o invólucro** do parâmetro (DataTime neste exemplo) devem corresponder ao parâmetro especificado no pipeline/atividade JSON. Na definição de procedimento armazenado, certifique-se de que **\@** é usado como prefixo para o parâmetro.

### <a name="create-a-data-factory"></a>Criar uma fábrica de dados
1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
2. Clique em **NEW** no menu esquerdo, clique em **Inteligência + Analytics** e clique em Data **Factory**.

    ![Nova fábrica de dados 1](media/data-factory-stored-proc-activity/new-data-factory.png)
3. Na nova lâmina de **fábrica de dados,** **insira o SProcDF** para o nome. Os nomes da Azure Data Factory são **globalmente únicos.** É necessário pré-afixar o nome da fábrica de dados com o seu nome, para permitir a criação bem sucedida da fábrica.

   ![Nova fábrica de dados 2](media/data-factory-stored-proc-activity/new-data-factory-blade.png)
4. Selecione a sua **subscrição Azure**.
5. Para **Grupo de Recursos**, siga um destes passos:
   1. Clique em **Criar novo** e insira um nome para o grupo de recursos.
   2. Clique **em Utilizar o grupo de** recursos existente.
6. Selecione a **localização** da fábrica de dados.
7. Selecione **Pin para painel de instrumentos** para que possa ver a fábrica de dados no painel de instrumentos da próxima vez que iniciar sessão.
8. Clique em **Criar** no painel **Nova fábrica de dados**.
9. Vê-se a fábrica de dados a ser criada no painel de **instrumentos** do portal Azure. Depois de ter criado a fábrica de dados com êxito, vê a página da fábrica de dados e o respetivo conteúdo.

   ![Página inicial da Fábrica de Dados](media/data-factory-stored-proc-activity/data-factory-home-page.png)

### <a name="create-an-azure-sql-linked-service"></a>Criar um serviço ligado Azure SQL
Depois de criar a fábrica de dados, cria um serviço ligado ao Azure SQL que liga a sua base de dados na Base de Dados Azure SQL, que contém a tabela de amostras e usp_sample procedimento armazenado, à sua fábrica de dados.

1. Clique em **Autor e implemente** na lâmina da **Fábrica de Dados** para **a SProcDF** lançar o Editor de Fábrica de Dados.
2. Clique em **Nova loja de dados** na barra de comando e escolha **Azure SQL Database**. Você deve ver o script JSON para criar um serviço ligado Azure SQL no editor.

   ![Nova loja de dados 1](media/data-factory-stored-proc-activity/new-data-store.png)
3. No roteiro JSON, faça as seguintes alterações:

   1. `<servername>`Substitua-o pelo nome do seu servidor.
   2. `<databasename>`Substitua-a pela base de dados na qual criou a tabela e o procedimento armazenado.
   3. `<username@servername>`Substitua-a pela conta de utilizador que tenha acesso à base de dados.
   4. `<password>`Substitua-a pela palavra-passe da conta de utilizador.

      ![Nova loja de dados 2](media/data-factory-stored-proc-activity/azure-sql-linked-service.png)
4. Para implementar o serviço ligado, clique em **Implementar** na barra de comando. Confirme que vê o AzureSqlLinkedService na vista da árvore à esquerda.

    ![vista de árvore com serviço ligado 1](media/data-factory-stored-proc-activity/tree-view.png)

### <a name="create-an-output-dataset"></a>Criar um conjunto de dados de saída
Deve especificar um conjunto de dados de saída para uma atividade de procedimento armazenado, mesmo que o procedimento armazenado não produza quaisquer dados. Isto porque é o conjunto de dados de saída que impulsiona o horário da atividade (com que frequência a atividade é executada - de hora a hora, diariamente, etc.). O conjunto de dados de saída deve utilizar um **serviço ligado** que se refira a uma Base de Dados Azure SQL ou a Azure Synapse Analytics ou a uma Base de Dados de Servidor SQL na qual pretende que o procedimento armazenado seja executado. O conjunto de dados de saída pode servir como forma de passar o resultado do procedimento armazenado para posterior processamento por outra atividade[(atividades de acorrentação](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline) no pipeline. No entanto, a Data Factory não escreve automaticamente a saída de um procedimento armazenado para este conjunto de dados. É o procedimento armazenado que escreve para uma tabela SQL que o conjunto de dados de saída aponta. Em alguns casos, o conjunto de dados de saída pode ser um **conjunto de dados falso** (um conjunto de dados que aponta para uma tabela que não detém realmente a saída do procedimento armazenado). Este conjunto de dados falso é utilizado apenas para especificar o calendário para a execução da atividade de procedimento armazenado.

1. **Clique... Mais** na barra de ferramentas, clique em **Novo conjunto de dados** e clique em **Azure SQL**. **Novo conjunto de dados** na barra de comando e selecione **Azure SQL**.

    ![vista de árvore com serviço ligado 2](media/data-factory-stored-proc-activity/new-dataset.png)
2. Copiar/colar o seguinte script JSON para o editor JSON.

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

### <a name="create-a-pipeline-with-sqlserverstoredprocedure-activity"></a>Criar um oleoduto com atividade de SqlServerStoredProcedure
Agora, vamos criar um oleoduto com uma atividade de procedimento armazenada.

Note as seguintes propriedades:

- A propriedade **tipo** está definida para **SqlServerStoredProcedure**.
- O **nome de Reserva Desconstrução armazenado** em propriedades do tipo está definido para **usp_sample** (nome do procedimento armazenado).
- A secção **de Parâmetros Deprocedures armazenados** contém um parâmetro chamado **DateTime**. O nome e o invólucro do parâmetro em JSON devem corresponder ao nome e invólucro do parâmetro na definição do procedimento armazenado. Se precisar de passar nulo para um parâmetro, utilize a sintaxe: `"param1": null` (todas as minúsculas).

1. **Clique... Mais** na barra de comando e clique em **Novo pipeline**.
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
3. Para implantar o gasoduto, clique em **Implementar** na barra de ferramentas.

### <a name="monitor-the-pipeline"></a>Monitorizar o pipeline
1. Clique em **X** para fechar os painéis do Editor do Data Factory e para regressar ao painel Data Factory e, em seguida, clique em **Diagrama**.

    ![azulejo de diagrama 1](media/data-factory-stored-proc-activity/data-factory-diagram-tile.png)
2. No **Diagrama View,** vê uma visão geral dos oleodutos e conjuntos de dados utilizados neste tutorial.

    ![diagrama azulejo 2](media/data-factory-stored-proc-activity/data-factory-diagram-view.png)
3. Na vista do diagrama, clique duas vezes no conjunto de dados `sprocsampleout` . Vês as fatias no estado de Ready. Deve haver cinco fatias porque uma fatia é produzida para cada hora entre a hora de início e o fim do JSON.

    ![diagrama azulejo 3](media/data-factory-stored-proc-activity/data-factory-slices.png)
4. Quando uma fatia estiver em **Estado de Ready,** esquiva uma `select * from sampletable` consulta na base de dados para verificar se os dados foram inseridos na tabela pelo procedimento armazenado.

   ![Dados de saída](./media/data-factory-stored-proc-activity/output.png)

   Consulte [o gasoduto monitor para](data-factory-monitor-manage-pipelines.md) obter informações detalhadas sobre a monitorização dos oleodutos da Azure Data Factory.

## <a name="specify-an-input-dataset"></a>Especificar um conjunto de dados de entrada
No processo de passagem, a atividade de procedimento armazenado não possui quaisquer conjuntos de dados de entrada. Se especificar um conjunto de dados de entrada, a atividade de procedimento armazenada não funciona até que a fatia do conjunto de dados de entrada esteja disponível (em estado de pronto). O conjunto de dados pode ser um conjunto de dados externo (que não é produzido por outra atividade no mesmo pipeline) ou um conjunto de dados interno que é produzido por uma atividade a montante (a atividade que executa antes desta atividade). Pode especificar vários conjuntos de dados de entrada para a atividade de procedimento armazenado. Se o fizer, a atividade de procedimento armazenado só funciona quando todas as fatias de conjunto de dados de entrada estiverem disponíveis (em estado de Pronto). O conjunto de dados de entrada não pode ser consumido no procedimento armazenado como parâmetro. É utilizado apenas para verificar a dependência antes de iniciar a atividade de procedimento armazenado.

## <a name="chaining-with-other-activities"></a>Acorrentar com outras atividades
Se pretender acorrentar uma atividade a montante com esta atividade, especifique a saída da atividade a montante como uma entrada desta atividade. Quando o fizer, a atividade de procedimento armazenado não funciona até que a atividade a montante esteja concluída e o conjunto de dados de saída da atividade a montante esteja disponível (em Estado de Pronto). Pode especificar conjuntos de dados de saída de múltiplas atividades a montante como conjuntos de dados de entrada da atividade do procedimento armazenado. Quando o fizer, a atividade de procedimento armazenado só funciona quando todas as fatias de conjunto de dados de entrada estão disponíveis.

No exemplo seguinte, a saída da atividade da cópia é: OutputDataset, que é uma entrada da atividade do procedimento armazenado. Por conseguinte, a atividade de procedimento armazenado não funciona até que a atividade da cópia esteja concluída e a fatia de Dataset de Saída esteja disponível (em estado pronto). Se especificar vários conjuntos de dados de entrada, a atividade de procedimento armazenada não funciona até que todas as fatias de conjunto de dados de entrada estejam disponíveis (em estado de pronto). Os conjuntos de dados de entrada não podem ser utilizados diretamente como parâmetros para a atividade do procedimento armazenado.

Para obter mais informações sobre atividades de acorrentamento, consulte [várias atividades num oleoduto](data-factory-create-pipelines.md#multiple-activities-in-a-pipeline)

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

Da mesma forma, para associar a atividade do procedimento de armazenação com as atividades a **jusante** (as atividades que funcionam após a conclusão da atividade do procedimento armazenado), especifique o conjunto de dados de saída da atividade do procedimento armazenado como uma entrada da atividade a jusante no oleoduto.

> [!IMPORTANT]
> Ao copiar dados para a Base de Dados Azure SQL ou para o SQL Server, pode configurar o **SqlSink** na atividade de cópia para invocar um procedimento armazenado utilizando a propriedade **sqlWriterStorDProcedureName.** Para obter mais informações, consulte [invocar o procedimento armazenado a partir da atividade de cópia](data-factory-invoke-stored-procedure-from-copy-activity.md). Para mais informações sobre a propriedade, consulte os seguintes artigos de conector: [Azure SQL Database,](data-factory-azure-sql-connector.md#copy-activity-properties) [SQL Server](data-factory-sqlserver-connector.md#copy-activity-properties).
> 
> Ao copiar dados da Base de Dados Azure SQL ou do SQL Server ou do Azure Synapse Analytics, pode configurar **o SqlSource** na atividade de cópia para invocar um procedimento armazenado para ler dados a partir da base de dados de origem utilizando a propriedade **sqlReaderStoredProcedureName.** Para mais informações, consulte os seguintes artigos de conector: [Azure SQL Database](data-factory-azure-sql-connector.md#copy-activity-properties), [SQL Server](data-factory-sqlserver-connector.md#copy-activity-properties), [Azure Synapse Analytics](data-factory-azure-sql-data-warehouse-connector.md#copy-activity-properties)

## <a name="json-format"></a>Formato JSON
Aqui está o formato JSON para definir uma Atividade de Procedimento Armazenado:

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

A tabela a seguir descreve estas propriedades JSON:

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| name | Nome da atividade |Yes |
| descrição |Texto que descreve para que a atividade é usada |No |
| tipo | Deve ser definido para: **SqlServerStoredProcedure** | Yes |
| entradas | Opcional. Se especificar um conjunto de dados de entrada, deve estar disponível (em estado 'Pronto') para que a atividade de procedimento armazenado seja executada. O conjunto de dados de entrada não pode ser consumido no procedimento armazenado como parâmetro. É utilizado apenas para verificar a dependência antes de iniciar a atividade de procedimento armazenado. |No |
| saídas | Deve especificar um conjunto de dados de saída para uma atividade de procedimento armazenado. O conjunto de dados de saída especifica o **calendário** para a atividade do procedimento armazenado (hora, semanais, mensal, etc.). <br/><br/>O conjunto de dados de saída deve utilizar um **serviço ligado** que se refira a uma Base de Dados Azure SQL ou a Azure Synapse Analytics ou a uma Base de Dados de Servidor SQL na qual pretende que o procedimento armazenado seja executado. <br/><br/>O conjunto de dados de saída pode servir como forma de passar o resultado do procedimento armazenado para posterior processamento por outra atividade[(atividades de acorrentação](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline) no pipeline. No entanto, a Data Factory não escreve automaticamente a saída de um procedimento armazenado para este conjunto de dados. É o procedimento armazenado que escreve para uma tabela SQL que o conjunto de dados de saída aponta. <br/><br/>Em alguns casos, o conjunto de dados de saída pode ser um **conjunto de dados falso**, que é utilizado apenas para especificar o calendário para a execução da atividade de procedimento armazenado. |Yes |
| nome de procedure armazenado |Especifique o nome do procedimento armazenado na Base de Dados Azure SQL, Azure Synapse Analytics ou SQL Server que é representado pelo serviço ligado que a tabela de saída utiliza. |Yes |
| parametrómetros de reserva armazenados |Especifique os valores para os parâmetros de procedimento armazenados. Se precisar de passar nulo para um parâmetro, utilize a sintaxe: "param1": nulo (todos os casos inferiores). Consulte a seguinte amostra para saber sobre a utilização deste imóvel. |No |

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

Agora, passe o parâmetro **cenário** e o valor da atividade do procedimento armazenado. A secção **typeProperties** na amostra anterior parece o seguinte corte:

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

**Conjunto de dados da Data Factory:**

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

**Gasoduto da Fábrica de Dados**

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
